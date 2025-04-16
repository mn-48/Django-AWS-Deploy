To deploy a React project with PM2 (Process Manager 2), you'll need to build your project and serve it as a static application since React is typically a client-side framework. Here’s a step-by-step guide:

---

### Prerequisites
1. **Node.js and npm** installed on your server.
2. **PM2** installed globally:
   ```bash
   npm install -g pm2
   ```
3. A built React project (i.e., the `build` folder after running `npm run build`).

---

### Steps to Deploy

#### 1. Build Your React Project
In your local React project directory:
```bash
npm run build
```
This generates an optimized production build in the `build` folder.

#### 2. Set Up a Server to Serve the Build
Since React's `build` folder contains static files (HTML, CSS, JS), you need a server to serve them. A simple way is to use a Node.js server with `serve` or `express`.

##### Option 1: Using `serve`
Install `serve` globally or locally:
```bash
npm install -g serve
```
Or locally in your project:
```bash
npm install serve --save
```

Then, serve the `build` folder:
```bash
serve -s build -l 9020
```
- `-s`: Ensures single-page app routing (redirects all requests to `index.html`).
- `-l 9020`: Specifies the port (e.g., 9020).

##### Option 2: Using Express
Create a simple `server.js` file in your project root:
```javascript
const express = require('express');
const path = require('path');
const app = express();

// Serve static files from the React build folder
app.use(express.static(path.join(__dirname, 'build')));

// Handle all routes by serving index.html (for SPA routing)
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'build', 'index.html'));
});

// Start server on port 9020
const PORT = process.env.PORT || 9020;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

Install Express:
```bash
npm install express
```

Update your `package.json` scripts:
```json
"scripts": {
  "start": "node server.js",
  "build": "react-scripts build"
}
```

#### 3. Transfer Files to Your Server
Upload your project (including the `build` folder, `package.json`, and `server.js` if using Express) to your server using:
- **SCP**:
  ```bash
  scp -r ./your-project-folder user@server-ip:/path/to/deploy
  ```
- **Git**: Push to a repository and pull it on the server.
- **FTP**: Use an FTP client like FileZilla.

#### 4. Install Dependencies on the Server
Navigate to your project folder on the server:
```bash
cd /path/to/deploy/your-project-folder
npm install --production
```

#### 5. Start the App with PM2
Use PM2 to manage your application process.

##### If Using `serve`:
```bash
pm2 start serve --name "react-app" -- -s build -l 9020
```

##### If Using Express:
```bash
pm2 start server.js --name "react-app"
```
- `--name "react-app"`: Names your PM2 process for easy management.

#### 6. Verify It’s Running
Check the status:
```bash
pm2 list
```
You should see your app listed with a status of "online."

Access it in your browser at `http://server-ip:9020`.

#### 7. Save the PM2 Process (Optional)
To ensure your app restarts on server reboot:
```bash
pm2 save
pm2 startup
```
Follow the instructions from `pm2 startup` to set up the startup script.

---

### Additional Tips
- **Environment Variables**: If your React app uses `.env` files, ensure they’re copied to the server and prefixed with `REACT_APP_` for client-side variables (e.g., `REACT_APP_API_URL`).
- **Logs**: Check logs with:
  ```bash
  pm2 logs react-app
  ```
- **Restart/Stop**: Manage your app with:
  ```bash
  pm2 restart react-app
  pm2 stop react-app
  pm2 delete react-app
  ```
- **Reverse Proxy (Optional)**: Use Nginx or Apache to route traffic from port 80/443 to 9020 and add SSL.

---

That’s it! Your React project should now be deployed and running with PM2 on port 9020. Let me know if you need help with any specific step!

---
---
---
তুমি যদি চাও যে, **PM2 দিয়ে ডিপ্লয় করা অ্যাপ্লিকেশনগুলো পিসি রিস্টার্টের পর আবার অটোমেটিক চালু হয়ে যাক**, তাহলে তোমাকে PM2-এর **startup script** ব্যবহার করতে হবে।

## ✅ ধাপ ১: PM2 স্টার্টআপ স্ক্রিপ্ট সেট করা

```bash
pm2 startup
```

এই কমান্ড রান করলে, এটা তোমাকে একটা কমান্ড দেখাবে যেমন:

```bash
sudo env PATH=$PATH:/home/your_user/.nvm/versions/node/vX.X.X/bin /home/your_user/.nvm/versions/node/vX.X.X/lib/node_modules/pm2/bin/pm2 startup systemd -u your_user --hp /home/your_user
```

এটা কপি করে টার্মিনালে রান করাও।

## ✅ ধাপ ২: বর্তমান অ্যাপ কনফিগারেশন সেভ করা

```bash
pm2 save
```

এটা করলে তোমার সব রানিং অ্যাপের লিস্ট সেভ হয়ে যাবে, এবং পরবর্তীতে পিসি রিস্টার্ট হওয়ার পরও PM2 ওই অ্যাপগুলো আবার চালু করে দেবে।

## ✅ এখন থেকে:

- যখনই তুমি নতুন অ্যাপ `pm2 start` দিয়ে চালাবে, এরপর `pm2 save` করো।
- পিসি রিস্টার্টের পর `pm2 resurrect` দরকার হবে না, কারণ এটি অটো হয়ে যাবে।

---

## ✅ চেক করতে চাইলে:
রিস্টার্টের পর লগ দেখো:
```bash
pm2 logs
```

---

🔁 যদি কখনও কনফিগ মুছে ফেলতে চাও:
```bash
pm2 unstartup
```

বলো ভাই, চাইলে আমি তোমার জন্য `deploy.sh` ফাইলও তৈরি করে দিতে পারি যাতে সব অটো হয়।
