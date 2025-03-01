### ✅ **To-Do List: Install React 18 and a Compatible `react-konva` Version**  

Follow these steps to ensure that your project uses **React 18** with a compatible version of `react-konva` (**v18.1.0**):

---

### **🟢 Step 1: Uninstall Current React and React DOM**
Before installing React 18, remove any existing versions of React:
```sh
npm uninstall react react-dom
```
or with Yarn:
```sh
yarn remove react react-dom
```

---

### **🟢 Step 2: Install React 18**
Now, install **React 18 and React DOM**:
```sh
npm install react@18 react-dom@18
```
or with Yarn:
```sh
yarn add react@18 react-dom@18
```

---

### **🟢 Step 3: Uninstall Incompatible `react-konva`**
If you have an incompatible version of `react-konva` (designed for React 19), remove it:
```sh
npm uninstall react-konva
```
or with Yarn:
```sh
yarn remove react-konva
```

---

### **🟢 Step 4: Install the Compatible `react-konva` Version (18.1.0)**
Now, install a version of `react-konva` that works with React 18:
```sh
npm install react-konva@18.1.0
```
or with Yarn:
```sh
yarn add react-konva@18.1.0
```

---

### **🟢 Step 5: Verify the Installed Versions**
To confirm everything is correctly installed, run:
```sh
npm list react react-dom react-konva
```
Expected output:
```
├── react@18.x.x
├── react-dom@18.x.x
└── react-konva@18.1.0
```

---

### **🟢 Step 6: Restart Your Project**
After installing everything, restart your development server:
```sh
npm start
```
or if using Next.js:
```sh
npm run dev
```

---

✅ **Now your project is running React 18 with `react-konva` 18.1.0, ensuring full compatibility and stability!** 🚀