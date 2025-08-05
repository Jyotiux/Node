# **Versioning in Express.js**
### **1 Understanding Version Numbers**
Express.js follows **semantic versioning**: `MAJOR.MINOR.PATCH`

Example: `4.18.2`
- **4 (Major Version)**: Significant updates, breaking changes. Code may not work on previous versions.
- **18 (Minor Version)**: Feature additions and security patches. Updating is recommended.
- **2 (Patch Version)**: Minor bug fixes. Updating is optional.

### **2 Installing Specific Versions**
To install Express.js:
```sh
npm install express
```
To install a specific version:
```sh
npm install express@4.18.2
```

### **3 Using Version Symbols in `package.json`**
- `^4.18.2` → Updates to `4.x.x`, but not `5.0.0` (keeps major version fixed).
- `~4.18.2` → Updates only the **patch** version (e.g., `4.18.3`, but not `4.19.0`).


