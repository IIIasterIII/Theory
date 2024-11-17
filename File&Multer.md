# File Upload Example with Express and Multer

This project demonstrates how to handle file uploads in a web application using **Express** and **Multer**. It includes a file input on the front-end for users to select files, and a back-end server to process and store those files.

## Features 🚀
- **File Upload**: Allows users to upload files from their device.
- **Multer Integration**: Handles `multipart/form-data` requests for file uploads.
- **Dynamic File Naming**: Files are saved with a unique timestamp to avoid conflicts.
- **Client-side Preview**: For image files, users can see a preview before upload.

## Technologies Used 🛠️
- **Frontend**: HTML, JavaScript (FileReader API)
- **Backend**: Node.js, Express, Multer
- **File Storage**: Local file system (uploads directory)

## Project Setup ⚙️

### Prerequisites
- Node.js
- npm (Node Package Manager)

### Installation 🔧

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/file-upload-example.git
   ````
2. Navigate to the project directory:
   ```bash
   cd file-upload-example
    ```
3. Install dependencies:
   ```bash
     npm install
   ```

Frontend File Input
In the HTML, users can select files using an <input> element with type="file". Here's an example of the file input field:

```html
<input type="file" accept="image/*,.pdf" id="fileInput" />
```

## JavaScript File Handling 📸
Once the file is selected, you can read it using the FileReader API to display a preview or process it further:
```js
document.getElementById('fileInput').addEventListener('change', function(event) {
  const file = event.target.files[0]; // Get the first file
  const reader = new FileReader();
  
  reader.onload = function(e) {
    const img = new Image();
    img.src = e.target.result; // Set the image source to the Data URL
    document.body.appendChild(img); // Display the image on the page
  };
  
  reader.readAsDataURL(file); // Read the file as a Data URL (for images)
});

```

Backend File Upload (Express and Multer) 📤
On the backend, we use Multer to handle file uploads. Here's the basic configuration to set up the server:

```js
const express = require('express');
const multer = require('multer');
const path = require('path');
const app = express();

// Configure file storage
const storage = multer.diskStorage({
  destination: function(req, file, cb) {
    cb(null, 'uploads/photos/'); // Directory for saving photos
  },
  filename: function(req, file, cb) {
    cb(null, Date.now() + path.extname(file.originalname)); // Unique filename
  }
});

// Set up Multer middleware
const upload = multer({ storage: storage });

// Handle file upload
app.post('/upload', upload.single('file'), (req, res) => {
  if (!req.file) {
    return res.status(400).send('No file selected.');
  }
  console.log(req.file); // Information about the uploaded file
  res.send('File uploaded successfully!');
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

Directory Setup 📁
Ensure that the uploads/photos/ directory exists. You can manually create it or use the following script to create it programmatically:

```js
const fs = require('fs');
const path = require('path');

const uploadDir = 'uploads/photos';

if (!fs.existsSync(uploadDir)) {
  fs.mkdirSync(uploadDir, { recursive: true });
}
```
