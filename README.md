# Face Authentication System

This project is a **real-time face authentication system** built using **Python, OpenCV, and Face Recognition**. It captures video from the webcam, detects faces, and classifies them as **"Known"** (marked with green) or **"Unknown"** (marked with red).

---

## 📌 Features

✅ **Real-time face detection and authentication**
✅ Supports **multiple known faces** stored in a folder
✅ Uses **HOG-based** face detection for efficiency
✅ **Frame skipping** for faster processing
✅ **Persistent face tracking** for smoother visualization
✅ Press **'q'** to exit the system

---

## 🛠️ Installation

### **1️⃣ Clone the Repository**

```bash
git clone [https://github.com/Shivam-1812/Face_Authentication.git](https://github.com/Shivam-1812/Face_Authentication.git)
cd Face_Authentication
2️⃣ Install Dependencies
Ensure Python 3.x is installed, then run:

Bash

pip install opencv-python face-recognition numpy
3️⃣ Prepare Known Faces
Create a folder named "known_faces" in the project directory. Add images of known people (JPEG/PNG format). The filenames should be their names (e.g., john_doe.jpg).

4️⃣ Run the Face Authentication System
Bash

python face_auth.py
🎯 How It Works
1️⃣ Loads known face images from the "known_faces" directory.
2️⃣ Captures video from the webcam.
3️⃣ Detects faces and compares them to known faces.
4️⃣ Displays a green box for known persons and a red box for unknown persons.
5️⃣ Continuously updates face states for smoother tracking.

⚙️ Configuration
You can modify these parameters inside the FaceAuthSystem class:

Parameter	Description	Default Value
tolerance	Face matching accuracy	0.55
frame_resize_ratio	Reduces frame size for faster processing	0.2
process_every_n_frames	Controls how often frames are processed	3
state_persistence_time	Time to keep face state for smoother UI	2 sec

Export to Sheets
🖥️ Example Output (Terminal)
Bash

Loading known faces...
Total known faces loaded: 3
Starting face authentication system... Press 'q' to quit.
When a face is detected:

Known Person → ✅ Green box & name displayed
Unknown Person → ❌ Red box with "Unknown"
🔄 Git Instructions
If you face an error while pushing changes to GitHub, follow these steps:

Fetch and Merge Changes
Bash

git pull origin main --rebase
git push -u origin main
Force Push (If Needed)
Bash

git push -u origin main --force
⚠ Warning: This will overwrite remote changes. Use with caution.

🚀 Future Enhancements
Add Deep Learning (CNN) for better accuracy.
Integrate Face Mask Detection.
Support for multiple cameras.
📜 License
This project is open-source and free to use.

💡 Note:
Ensure your webcam is working before running the script.
The first-time recognition may take longer due to face encoding.
You can enhance accuracy by using high-quality images for known faces.
