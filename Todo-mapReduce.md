# Hadoop Word Count Project

## Introduction
This project demonstrates a Word Count program implemented in both Java and Python using Apache Hadoop. The program processes text data to count the frequency of each word.

## Prerequisites
- Apache Hadoop installed and configured.
- Python 3.x installed.

---

## HDFS Setup

### Create a Directory in HDFS
```bash
hdfs dfs -mkdir javafile
```

### Verify Directory Creation
```bash
hdfs dfs -ls
```

### Grant Permissions to the Directory
```bash
hdfs dfs -chmod 777 javafile
```

### Upload the Data File to HDFS
```bash
hdfs dfs -put purchases.txt javafile
```

### Verify Uploaded Files
```bash
hdfs dfs -ls javafile
```

### Display the Data File Content
```bash
hdfs dfs -cat javafile/purchases.txt
```

---

## Java Implementation

### Create Directory for Java Files
```bash
mkdir javafile 
cd javafile
```

### Create Java Files
- `WC_Mapper.java`
- `WC_Reducer.java`
- `WC_Runner.java`

### Java Code

#### WC_Mapper.java
```java
import java.io.IOException;
import java.util.StringTokenizer;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reporter;

public class WC_Mapper extends MapReduceBase implements Mapper<LongWritable, Text, Text, IntWritable> {
    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(LongWritable key, Text value, OutputCollector<Text, IntWritable> output, Reporter reporter) throws IOException {
        String line = value.toString();
        StringTokenizer tokenizer = new StringTokenizer(line);
        while (tokenizer.hasMoreTokens()) {
            word.set(tokenizer.nextToken());
            output.collect(word, one);
        }
    }
}
```

#### WC_Reducer.java
```java
import java.io.IOException;
import java.util.Iterator;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;

public class WC_Reducer extends MapReduceBase implements Reducer<Text, IntWritable, Text, IntWritable> {
    public void reduce(Text key, Iterator<IntWritable> values, OutputCollector<Text, IntWritable> output, Reporter reporter) throws IOException {
        int sum = 0;
        while (values.hasNext()) {
            sum += values.next().get();
        }
        output.collect(key, new IntWritable(sum));
    }
}
```

#### WC_Runner.java
```java
import java.io.IOException;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.TextInputFormat;
import org.apache.hadoop.mapred.TextOutputFormat;

public class WC_Runner {
    public static void main(String[] args) throws IOException {
        if (args.length != 2) {
            System.err.println("Usage: WC_Runner <input path> <output path>");
            System.exit(-1);
        }

        JobConf conf = new JobConf(WC_Runner.class);
        conf.setJobName("WordCount");

        conf.setOutputKeyClass(Text.class);
        conf.setOutputValueClass(IntWritable.class);

        conf.setMapperClass(WC_Mapper.class);
        conf.setCombinerClass(WC_Reducer.class);
        conf.setReducerClass(WC_Reducer.class);

        conf.setInputFormat(TextInputFormat.class);
        conf.setOutputFormat(TextOutputFormat.class);

        FileInputFormat.setInputPaths(conf, new Path(args[0]));
        FileOutputFormat.setOutputPath(conf, new Path(args[1]));

        JobClient.runJob(conf);
    }
}
```

### Compile Java Files
```bash
javac -cp $(hadoop classpath) -d . *.java
```

### Create JAR File
```bash
jar cf Results.jar *.class
```

### Run the JAR File on Hadoop
```bash
hadoop jar ./Results.jar WC_Runner javafile/purchases.txt javafile/Result_out
```

### View Output
```bash
hdfs dfs -cat javafile/Result_out/part-00000
```

---

## Python Implementation

### Mapper Code (`mapper.py`)
```python
import sys
for line in sys.stdin:
    line = line.strip()
    words = line.split()
    for word in words:
        print('{0}\t{1}'.format(word, 1))
```

### Reducer Code (`reducer.py`)
```python
from operator import itemgetter
import sys

current_word = None
current_count = 0
word = None

for line in sys.stdin:
    line = line.strip()
    word, count = line.split('\t', 1)
    try:
        count = int(count)
    except ValueError:
        continue

    if current_word == word:
        current_count += count
    else:
        if current_word:
            print('{0}\t{1}'.format(current_word, current_count))
        current_count = count
        current_word = word

if current_word == word:
    print('{0}\t{1}'.format(current_word, current_count))
```

### Run Python Scripts

#### Local Execution
```bash
cat purchases.txt | python mapper.py | sort -k1,1 | python reducer.py
```

#### Hadoop Execution
```bash
hadoop jar /path/to/hadoop-streaming.jar \
  -input /path/to/input \
  -output /path/to/output \
  -mapper mapper.py \
  -reducer reducer.py
```

### View Result
```bash
hdfs dfs -cat /path/to/output/part-00000
