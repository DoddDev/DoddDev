# Portfolio
Welcome to my Portfolio! Here you will find an overview of the applications I have created for [Mission Room](https://missionroom.com/)

# Touch
### Overview
Touch is an application that transforms Mission Room Display Systems into a fully interactive Touch screen System. With Touch, users can control the display without the use of a mouse. By using a specialized infrared pen on one of the screens, an infrared camera detects the light emitted, calculates the precise location, and translates it into a mouse coordinate, moving the cursor to the desired point on the screen.
### Technical Description
Touch utilises 3 Machine vision cameras provided by FLIR. To operate, Touch requires the use of the [Spinnaker SDK](https://www.flir.co.uk/products/spinnaker-sdk/?vertical=machine+vision&segment=iis), which adheres to the GenICam standard. This SDK enables full access to all camera nodes and facilitates the retrieval of frames from the camera.<br/><br/>
After a frame is received, Touch uses a blob detection algorithm to identify any detected light. If light is detected the application calculates its center point. Before the mouse can be moved, Touch requires calibration. This process involves displaying a window on each screen with 9 crosshairs on each screen. The user is prompted to press the infrared pen to each crosshair. Once the user has pressed all crosshairs, the application calculates the relationship between the detected light and the crosshair positions using homography. The results of the calculations are stored on the computer, allowing Touch to access the calibration data when the user opens the application.<br/><br/>
When Touch detects a new blob, the application utilizes the homography matrix to accurately translate the blob's position into on-screen coordinates, moving the mouse cursor to the corresponding location.<br/><br/>
Touch also features a GUI, providing users with easy access to essential controls. Through this interface, users can restart the cameras, calibrate them, adjust camera exposure settings, and enable or disable the mouse movement functionality. Touch includes an advanced view that shows the live camera feed, camera runtime, detected blob coordinates, and calibration status, allowing users to efficiently track and manage system performance. <br/><br/>
Also in the Touch GUI includes a raw camera feed that displays the camera’s view of the screens, serving as a debugging tool to verify camera coverage.<br/><br/>
Touch offers customizable settings, allowing users to adjust configurations to optimize performance for their specific environment.
### Challenges and Solutions
Mission Room installs display systems in a variety of environments, including head offices, site cabins, and event gazebos, each with unique lighting conditions from both artificial and natural sources. Touch is highly sensitive to light, as it relies on infrared light for its operation. Given the sensitivity to light and the necessity for optimal camera settings, manually adjusting exposure, gain, and gamma through the GUI proved impractical for users. To address this issue, I developed the "Auto Camera Brightness" feature. This solution initially sets all camera settings to their maximum values and then gradually reduces them. The process continues until the cameras no longer detect light, ensuring optimal camera settings for varying lighting conditions. <br/><br/>
Another challenge was light reflecting from one screen to the edge of adjacent screens. To address the issue of the mouse inadvertently moving between screens due to reflections, I developed the "Select Boundary" function. When the user taps a screen with the pen, that screen becomes active, allowing the pen to operate within its borders. If the user taps the edge of a non-active screen, the touch input is ignored, preventing unintended cursor movement between screens. <br/><br/>

### Role
When I first joined Mission Room, Touch was a minimal viable product created for a specific customer. My role was to independently redesign and redevelop the application. This was a significant challenge, especially as I had just finished university. I dedicated all my drive to developing Touch, focusing my learning exclusively on application development and applied all my previous knowledge of image processing to develop the core functionality of the application. As of now, Touch is utilized across all Mission Room systems for companies involved in infrastructure projects, as well as for training and onboarding purposes.

# Touch Camera Intrinsic Calibration application
### Overview
The Cameras that are used in the Touch application are mounted to the roof of the display systems. For Touch to work the cameras are required to view the entirety of the screens. Standard camera lenses often failed to capture the full screens, so fisheye lenses were adopted to ensure complete coverage. However, using fisheye lenses introduced visual distortion in the captured frames. For accurate calculation of the homography matrix in Touch, the frames need to be undistorted and appear flat. The application utilises OpenCV's fisheye undistort methods to correct the visual distortion in the images captured by the cameras, ensuring accurate, undistorted frames for Touch.
### Technical Description
To undistort the camera frames, access to the cameras is required, which is achieved using the [Spinnaker SDK](https://www.flir.co.uk/products/spinnaker-sdk/?vertical=machine+vision&segment=iis). This SDK provides comprehensive access to all camera nodes and facilitates the retrieval of frames for processing. <br/><br/>
After setting up the camera and successfully retrieving frames, the next step is to correct image distortion. To undistort a fisheye image, a chessboard pattern is required. This pattern is widely used for camera calibration and lens distortion correction, as it offers a well-defined, regular pattern that can be easily detected and analyzed in images. <br/><br/>
Once a chessboard is detected using OpenCV, the frame is added to a list. After collecting 10 such images, the undistortion calculation is performed.<br/><br/>
### SHOW CODE
The undistortion process returns two matrices, which are then stored in the cameras' onboard memory. When Touch starts up, it accesses this stored data to calculate the homography matrix for mouse movements.<br/><br/>
### SHOW GUI

# Touch Gestures
Complete when finished 

# GSM-R Scenario

# Meeting Recorder

# 360 Image Face Blur
