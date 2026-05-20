# EXPERIMENT-07-INTERFACING-CAMERA-MODULE-ON-EDGE-COMPUTER-FOR-OCCUPANCY-DETECTION-


### AIM:
To interface a USB/CSI camera module with an edge computing platform (e.g., Raspberry Pi, Jetson Nano, etc.) and implement an occupancy detection system using the Histogram of Oriented Gradients (HOG) algorithm.

### Apparatus/Software Required:
S. No.	Equipment / Software	Specification
1.	Edge Computing Device	Raspberry Pi 4 / Jetson Nano
2.	Camera Module	USB Webcam / Pi Camera Module
3.	Operating System	Raspbian OS / Ubuntu
4.	Programming Language	Python 3.x
5.	Libraries	OpenCV, imutils, NumPy
6.	Display Output	HDMI Monitor / VNC Viewer

### Theory:
Histogram of Oriented Gradients (HOG) is a feature descriptor used in computer vision and image processing for the purpose of object detection. It counts occurrences of gradient orientation in localized portions of an image. HOG descriptors are particularly useful for detecting humans (pedestrians) in static images or video frames.

Steps involved in HOG-based Occupancy Detection:

Capture frames from the camera.

Resize and preprocess the image.

Use a pre-trained HOG descriptor with a linear SVM to detect people in the image.

Annotate the image with bounding boxes where people are detected.

Display or store the result.

Circuit Diagram / Setup:
Connect the USB camera to the edge computer via a USB port.

Power on the edge device and boot into the OS.

Ensure necessary Python libraries are installed.

### Procedure:
Set up the edge device with a monitor or SSH/VNC connection.

Connect and verify the camera using commands like ls /dev/video* or vcgencmd get_camera.

Install required libraries:

bash
Copy
Edit
pip install opencv-python imutils numpy
Write the Python code to initialize the camera and implement the HOG algorithm.

Run the code and verify that the system detects human presence and draws bounding boxes.

 ###  Python Code:
```
import cv2
import imutils

# ==========================================
# Android DroidCam URL
# ==========================================

# Replace with your Android phone IP
url = "http://172.17.147.55:4747/video"

# Open video stream
cap = cv2.VideoCapture(url)

# Check camera connection
if not cap.isOpened():
    print("Unable to connect to Android camera stream")
    exit()

print("Android Camera Connected Successfully")

# ==========================================
# Initialize HOG Person Detector
# ==========================================

hog = cv2.HOGDescriptor()
hog.setSVMDetector(cv2.HOGDescriptor_getDefaultPeopleDetector())

# ==========================================
# Main Loop
# ==========================================

while True:

    # Read frame
    ret, frame = cap.read()

    if not ret:
        print("Failed to receive frame")
        break

    # Resize frame
    frame = imutils.resize(frame, width=640)

    # ==========================================
    # Detect People
    # ==========================================

    rects, weights = hog.detectMultiScale(
        frame,
        winStride=(4, 4),
        padding=(8, 8),
        scale=1.05
    )

    person_count = 0

    # Draw rectangles
    for (x, y, w, h) in rects:

        person_count += 1

        cv2.rectangle(
            frame,
            (x, y),
            (x + w, y + h),
            (0, 255, 0),
            2
        )

        cv2.putText(
            frame,
            "Person",
            (x, y - 10),
            cv2.FONT_HERSHEY_SIMPLEX,
            0.5,
            (0, 255, 0),
            2
        )

    # ==========================================
    # Occupancy Status
    # ==========================================

    if person_count > 0:
        status = "OCCUPIED"
        color = (0, 0, 255)
    else:
        status = "EMPTY"
        color = (255, 0, 0)

    cv2.putText(
        frame,
        f"Status : {status}",
        (20, 40),
        cv2.FONT_HERSHEY_SIMPLEX,
        0.8,
        color,
        2
    )

    cv2.putText(
        frame,
        f"Persons : {person_count}",
        (20, 80),
        cv2.FONT_HERSHEY_SIMPLEX,
        0.8,
        (255, 255, 0),
        2
    )

    # ==========================================
    # Show Output
    # ==========================================

    cv2.imshow("Android Occupancy Detection", frame)

    # Press q to exit
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

# ==========================================
# Cleanup
# ==========================================

cap.release()
cv2.destroyAllWindows()
```
### SCREEN SHOTS OF OUTPUT 


<img width="1919" height="1079" alt="Screenshot 2026-05-20 104159" src="https://github.com/user-attachments/assets/4ba79a5f-2b12-4ee0-a9b7-99b1e3b98b81" />



### RASPI INTERFACE 

<img width="575" height="1280" alt="WhatsApp Image 2026-05-20 at 10 44 34 AM" src="https://github.com/user-attachments/assets/48fc5e2b-07ad-4781-afb1-b5d201d02e4c" />



### Result:
Occupancy detection using the HOG algorithm was successfully implemented. The system was able to identify and highlight human presence in real-time video streams.
