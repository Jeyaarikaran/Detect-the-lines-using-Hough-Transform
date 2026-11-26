# Lane Detection Using OpenCV and Hough Transform

## Developed By

### Name:JEYAARIKARAN P
### Register No:212224240064

---

## Aim

To implement a basic lane detection pipeline using OpenCV by completing missing code segments at specified locations.

---

## Learning Objective

- Understand each stage of image processing.
- Learn how to build a complete computer vision pipeline.
- Practice writing code in guided sections.

**Important Instruction:**

- Write code **ONLY in places marked as `# Your Code Here`**.
- Do NOT modify any other part of the code.

---

## Software Used

- Anaconda – Python 3.7 (Laboratory Specification)
- Jupyter Notebook / VS Code
- OpenCV (cv2)
- NumPy
- Matplotlib

---

## Algorithm & Explanation

---

### Step 1: Import Libraries

Import the required libraries for image processing, numerical operations and visualization.

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
```

---

### Step 2: Read the Image

Load the input highway image using OpenCV.

```python
# Read the image using OpenCV
# Your Code Here

image = cv2.imread(r"C:\Users\admin\Downloads\highway.png")

if image is None:
    raise FileNotFoundError("Image not found")

print("Image loaded successfully")
```

#### Output

```text
Image loaded successfully
```

---

### Step 3: Convert to Grayscale

Convert the original colour image into a single-channel grayscale image.

```python
# Convert to grayscale
# Your Code Here

gray_image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
```

---

### Step 4: Display Images

Display the original image and grayscale image using Matplotlib.

```python
plt.figure(figsize=(10,5))

# Your Code Here

plt.subplot(1, 2, 1)
plt.imshow(cv2.cvtColor(image, cv2.COLOR_BGR2RGB))
plt.title("Original Image")
plt.axis('off')

plt.subplot(1, 2, 2)
plt.imshow(gray_image, cmap='gray')
plt.title("Grayscale Image")
plt.axis('off')

plt.show()
```

#### Output: Original Image

<img width="443" height="308" alt="Original Image" src="https://github.com/user-attachments/assets/e7f51ebe-d76a-4e6c-89ec-f186331da469" />

#### Output: Grayscale Image

<img width="480" height="327" alt="Grayscale Image" src="https://github.com/user-attachments/assets/9f7bfe90-3fb6-43d3-93fb-f606e74bdb4c" />

---

### Step 5: Thresholding

Apply binary thresholding to separate bright regions from the background.

```python
# Apply thresholding
# Your Code Here

threshold = cv2.threshold(
    gray_image, 150, 255, cv2.THRESH_BINARY
)[1]

plt.imshow(threshold, cmap='gray')
plt.title("Thresholded Image")
plt.axis('off')
plt.show()
```

#### Output: Thresholded Image

<img width="605" height="420" alt="Thresholded Image" src="https://github.com/user-attachments/assets/95b34ba4-effb-4670-a87b-aba229c19d55" />

---

### Step 6: Region of Interest (ROI)

Apply ROI masking to select the relevant road region and eliminate unwanted background regions.

```python
# ROI Masking
# Do not modify

height, width = threshold.shape

mask = np.zeros_like(threshold)

vertices = np.array([[
    (0, height - 1),
    (int(width * 0.45), int(height * 0.55)),
    (int(width * 0.55), int(height * 0.55)),
    (width - 1, height - 1)
]], dtype=np.int32)

cv2.fillPoly(mask, vertices, 255)

masked_image = cv2.bitwise_and(threshold, mask)

plt.imshow(masked_image, cmap='gray')
plt.title("ROI Masked Image")
plt.axis('off')
plt.show()
```

#### Output: ROI Masked Image

<img width="667" height="417" alt="ROI Masked Image" src="https://github.com/user-attachments/assets/079c2d39-dbd9-4dc9-9ba8-3f2d2274eda2" />

---

### Step 7: Edge Detection (Canny)

Apply the Canny edge detection algorithm to identify edges within the selected region of interest.

```python
# Perform Edge Detection
# Your Code Here

edges = cv2.Canny(masked_image, 50, 150)

plt.imshow(edges, cmap='gray')
plt.title("Edge Detected Image")
plt.axis('off')
plt.show()
```

#### Output: Edge Detected Image

<img width="627" height="412" alt="Canny Edge Detection" src="https://github.com/user-attachments/assets/9836a65c-41ba-417f-a058-593fa15ae6ab" />

---

### Step 8: Gaussian Blur

Apply Gaussian smoothing to the edge-detected image.

```python
# Apply Gaussian Blur
# Your Code Here

blurred_image = cv2.GaussianBlur(edges, (5, 5), 0)

plt.imshow(blurred_image, cmap='gray')
plt.title("Smoothed Image")
plt.axis('off')
plt.show()
```

#### Output: Smoothed Image

<img width="593" height="411" alt="Gaussian Smoothed Image" src="https://github.com/user-attachments/assets/e9e8c159-6ea7-4e3f-98f5-774bfad7be02" />

---

### Step 9: Hough Transform

Use the Probabilistic Hough Transform to detect straight line segments from the processed image.

```python
# Detect lines using Hough Transform
# Your Code Here

lines = cv2.HoughLinesP(
    blurred_image,
    1,
    np.pi / 180,
    50,
    minLineLength=40,
    maxLineGap=20
)

line_image = np.zeros_like(image)

if lines is None:
    lines = np.empty((0, 1, 4), dtype=np.int32)

for line in lines:
    x1, y1, x2, y2 = line[0]

    cv2.line(
        line_image,
        (x1, y1),
        (x2, y2),
        (0, 255, 0),
        2
    )

plt.imshow(cv2.cvtColor(line_image, cv2.COLOR_BGR2RGB))
plt.title("Detected Lines")
plt.axis('off')
plt.show()

print("Number of detected lines:", len(lines))
```

#### Output: Detected Lines

<img width="647" height="446" alt="Hough Transform Detected Lines" src="https://github.com/user-attachments/assets/aff512ff-8430-4b85-b1cf-496605cb2a3e" />

#### Recorded Execution Output

```text
Number of detected lines: 106
```

---

### Step 10: Lane Detection Logic

Filter nearly horizontal line segments and highlight candidate lane markings on the original highway image.

```python
# Lane Detection Logic
# Do not modify

final_image = image.copy()

for line in lines:
    x1, y1, x2, y2 = line[0]

    if abs(y2 - y1) > 0.5 * abs(x2 - x1):

        cv2.line(
            final_image,
            (x1, y1),
            (x2, y2),
            (0, 255, 0),
            3
        )

plt.figure(figsize=(10,5))
plt.imshow(cv2.cvtColor(final_image, cv2.COLOR_BGR2RGB))
plt.title("Final Lane Detection Output")
plt.axis('off')
plt.show()
```

#### Output: Final Lane Detection Output

<img width="695" height="456" alt="Final Lane Detection Output" src="https://github.com/user-attachments/assets/9e9c12cb-5844-4c09-9351-aa03527499e4" />

---

---

## Instructions

- Fill ONLY in `# Your Code Here` sections.
- Do NOT change existing code.
- Execute each notebook cell step-by-step.
- Verify all intermediate and final outputs.
- Ensure all required libraries are installed.
- Update the input image path when executing on another computer.

---

## Result

Thus, the lane detection pipeline was implemented using OpenCV, NumPy and Matplotlib by completing the missing code sections.

The recorded notebook execution detected 106 line segments using the Hough Transform. Candidate road lane markings were highlighted on the original highway image after filtering nearly horizontal lines.
