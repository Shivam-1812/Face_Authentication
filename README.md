import cv2
import face_recognition
import os
import numpy as np
import time

class FaceAuthSystem:
    def __init__(self, known_faces_dir):
        self.known_face_encodings = []
        self.known_face_names = []
        self.tolerance = 0.55  # Adjusted for faster matches
        self.frame_resize_ratio = 0.2  # Reduce frame size for faster processing
        self.process_every_n_frames = 3  # Process every 3rd frame
        self.face_state = {}  # Store face states for persistence
        self.state_persistence_time = 2  # Time in seconds to keep the state
        self.load_known_faces(known_faces_dir)

    def load_known_faces(self, known_faces_dir):
        """Load known faces from the specified directory."""
        print("\nLoading known faces...")
        for filename in os.listdir(known_faces_dir):
            if filename.endswith((".jpg", ".jpeg", ".png")):
                image_path = os.path.join(known_faces_dir, filename)
                image = face_recognition.load_image_file(image_path)
                encodings = face_recognition.face_encodings(image)
                if encodings:
                    self.known_face_encodings.append(encodings[0])
                    self.known_face_names.append(os.path.splitext(filename)[0])
        print(f"\nTotal known faces loaded: {len(self.known_face_names)}")

    def run_authentication(self):
        """Run real-time face authentication using webcam."""
        video_capture = cv2.VideoCapture(0)
        if not video_capture.isOpened():
            print("Error: Could not open video capture device")
            return

        frame_count = 0
        print("\nStarting face authentication system... Press 'q' to quit.")
        while True:
            ret, frame = video_capture.read()
            if not ret:
                print("Error: Couldn't read frame")
                break

            current_time = time.time()

            # Process every `n` frames
            if frame_count % self.process_every_n_frames == 0:
                small_frame = cv2.resize(frame, (0, 0), fx=self.frame_resize_ratio, fy=self.frame_resize_ratio)
                rgb_small_frame = cv2.cvtColor(small_frame, cv2.COLOR_BGR2RGB)
                face_locations = face_recognition.face_locations(rgb_small_frame, model="hog")
                face_encodings = face_recognition.face_encodings(rgb_small_frame, face_locations)

                for (top, right, bottom, left), face_encoding in zip(face_locations, face_encodings):
                    matches = face_recognition.compare_faces(self.known_face_encodings, face_encoding, self.tolerance)
                    name = "Unknown"
                    color = (0, 0, 255)  # Red for unknown faces

                    if True in matches:
                        first_match_index = matches.index(True)
                        name = self.known_face_names[first_match_index]
                        color = (0, 255, 0)  # Green for known faces

                    # Scale face locations back to original size
                    top, right, bottom, left = [x * int(1 / self.frame_resize_ratio) for x in [top, right, bottom, left]]

                    # Store the state with a timestamp
                    self.face_state[(top, right, bottom, left)] = {
                        "name": name,
                        "color": color,
                        "timestamp": current_time
                    }

            frame_count += 1

            # Draw persistent states
            self.draw_persistent_states(frame, current_time)

            cv2.imshow('Face Authentication System', frame)

            if cv2.waitKey(1) & 0xFF == ord('q'):
                break

        video_capture.release()
        cv2.destroyAllWindows()

    def draw_persistent_states(self, frame, current_time):
        """Draw persistent states of faces on the frame."""
        expired_keys = []

        for (top, right, bottom, left), state in self.face_state.items():
            # Remove expired states
            if current_time - state["timestamp"] > self.state_persistence_time:
                expired_keys.append((top, right, bottom, left))
                continue

            # Draw rectangle and name
            name = state["name"]
            color = state["color"]
            cv2.rectangle(frame, (left, top), (right, bottom), color, 2)
            cv2.rectangle(frame, (left, bottom - 35), (right, bottom), color, cv2.FILLED)
            cv2.putText(frame, name, (left + 6, bottom - 6), cv2.FONT_HERSHEY_DUPLEX, 0.6, (255, 255, 255), 1)

        # Remove expired states from the dictionary
        for key in expired_keys:
            del self.face_state[key]

if __name__ == "__main__":
    # Generate a README file
    readme_content = """\
# Face Authentication System

This project is a **real-time face authentication system** built using **Python, OpenCV, and Face Recognition**. It detects and recognizes faces from a webcam and marks them as **"Known"** (green) or **"Unknown"** (red) based on stored face data.

## Features
- Real-time face detection and authentication.
- Supports multiple known faces stored in a folder.
- Uses **HOG-based** face detection for efficiency.
- **Frame skipping** to improve performance.
- **Persistent face state** for smoother tracking.
- Press **'q'** to exit the program.

## Installation

### 1. Clone the Repository
```sh
git clone https://github.com/yourusername/FaceAuthSystem.git
cd FaceAuthSystem
