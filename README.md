# Smart Traffic Project

This project contains a Python script (provided as a Jupyter Notebook `maincode.ipynb`) designed to process video footage of traffic. It uses deep learning and optical character recognition (OCR) to detect vehicles, estimate their direction of travel, and extract timestamps from the video frame to log structured traffic data.

## Features

- **Vehicle Detection:** Uses a custom-trained **YOLOv8** model to recognize different classes of vehicles.
- **Timestamp Extraction:** Crops a predefined Region of Interest (ROI) from the video frame and applies **EasyOCR** to extract the date and time printed on the video feed.
- **Direction Tracking:** Tracks the position of each vehicle frame-by-frame using centroid calculations to determine if the vehicle is moving "up" or "down".
- **Traffic Logging:** Outputs the gathered data (Date, Time, Vehicle Class, Direction) into a CSV file (`output6.csv`), avoiding duplicate logging for the same tracked vehicle.

## Prerequisites

To run this code, you will need **Python 3.x** and the following libraries:

```bash
pip install opencv-python numpy pandas easyocr ultralytics
```

## How It Works

1. **Initialization:** The script loads the YOLOv8 model from a specified local `.pt` weights file and initializes an empty Pandas DataFrame for logging.
2. **Video Processing:** A loop continuously reads frames from the specified video path using OpenCV.
3. **Timestamp OCR:** Each frame undergoes cropping using specific ROI coordinates. EasyOCR extracts the date and time string from this crop and parses it into Python `datetime` objects.
4. **Detection & Tracking:** YOLOv8 runs inference on the full frame to detect vehicles. A basic tracking algorithm calculates the bounding box centroids and records the trajectory history of overlapping boxes to establish an ID.
5. **Direction Estimation:** Based on the shift in the `y`-coordinates of a tracked vehicle's centroid over recent frames, the script assigns a direction ("up", "down", or "Unknown").
6. **Data Output:** New distinct detections are appended to the CSV file continuously.

## Usage

1. **Configure File Paths:** Open `maincode.ipynb` and ensure the following paths are correctly configured for your environment:
   - YOLOv8 weights file: Modify `model = YOLO('...', ...)` to point to your `best.pt` file.
   - Video Input: Modify `process_video('...')` at the bottom of the script to point to the traffic video you wish to process.
2. **Adjust Details (Optional):**
   - **ROI coordinates:** You may need to update the `roi_coords` to match the location of the timestamp for your specific video layout.
   - **CSV Name:** Change `csv_file = 'output6.csv'` if you want to output to a different file.
3. **Run the Notebook:** Execute the cells in your Jupyter environment. Press the `q` key with the video window active to terminate the script early.

## Known Limitations / Troubleshooting

- **Hardcoded Coordinates:** The region from which the date/time is extracted (`roi_coords`) is currently hardcoded and assumes a specific video resolution and overlay format. You must update this if using videos from different camera angles or setups.
- **OCR Accuracy:** EasyOCR relies on good contrast and readable text formatting to accurately extract the date and time.

## Future Enhancements / Upgrades

- **Dynamic ROI Detection:** Instead of hardcoding the region of interest for the timestamp, implement a pre-processing step to automatically locate the date/time text block within the video frame.
- **Improved Tracking Algorithm:** Upgrade the basic centroid-based tracker to a more robust multi-object tracking algorithm like **DeepSORT** or **ByteTrack** for better handling of vehicle occlusions and complex traffic patterns.
- **Speed Estimation:** Calibrate the camera's perspective to measure real-world distances and incorporate speed estimation for each detected vehicle.
- **Real-Time Data Dashboard:** Connect the continuously updated output (currently saved to CSV) to a live data visualization dashboard (e.g., using Streamlit, Dash, or Grafana) to monitor traffic flow in real time.
- **Lane Identification:** Incorporate lane detection techniques to specify the exact lane each vehicle is traveling in, in addition to the overall "up" or "down" direction.
- **Performance Optimization:** Optimize the video processing pipeline (e.g., using multi-threading, asynchronous OCR processing, or TensorRT for YOLO inference) to enable real-time analysis of high-resolution, high-framerate video feeds.
