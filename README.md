# Portfolio
Welcome to my Portfolio! Here you will find an overview of the .NET applications I have created for [Mission Room](https://missionroom.com/)

# Touch
### Overview
Touch is an application that transforms Mission Room Display Systems into a fully interactive Touch screen System. With Touch, users can control the display without the use of a mouse. By using a specialized infrared pen on one of the screens, an infrared camera detects the light emitted, calculates the precise location, and translates it into a mouse coordinate, moving the cursor to the desired point on the screen. Touch allows users to perform left click and right clicks on the display sytems.
### Description
Touch utilises 3 Machine vision cameras provided by FLIR. To operate, Touch requires the use of the [Spinnaker SDK](https://www.flir.co.uk/products/spinnaker-sdk/?vertical=machine+vision&segment=iis), which adheres to the GenICam standard. This SDK enables full access to all camera nodes and facilitates the retrieval of frames from the camera.<br/><br/>
After a frame is received, Touch uses a blob detection algorithm to identify any detected light. If light is detected the application calculates its center point. Before the mouse can be moved, Touch requires calibration. This process involves displaying a window on each screen with 9 crosshairs on each screen. The user is prompted to press the infrared pen to each crosshair. Once the user has pressed all crosshairs, the application calculates the relationship between the detected light and the crosshair positions using homography. The results of the calculations are stored on the computer, allowing Touch to access the calibration data when the user opens the application.<br/><br/>
When Touch detects a new blob, the application utilizes the homography matrix to accurately translate the blob's position into on-screen coordinates, moving the mouse cursor to the corresponding location. When the application moves the mouse, it generates a left mouse down event. When no light is detected, it triggers a left mouse up event. This functionality enables the user to perform a left click by tapping the pen on the screen, or to highlight text or objects by dragging the pen across the screen. If the user holds the pen in the same location on the screen, it triggers a right mouse click event.<br/><br/>
Touch also features a GUI, providing users with easy access to essential controls. Through this interface, users can restart the cameras, calibrate them, adjust camera exposure settings, and enable or disable the mouse movement functionality. Touch includes an advanced view that shows the live camera feed, camera runtime, detected blob coordinates, and calibration status, allowing users to efficiently track and manage system performance. <br/><br/>
Also in the Touch GUI includes a raw camera feed that displays the camera’s view of the screens, serving as a debugging tool to verify camera coverage.<br/><br/>
Touch offers customizable settings, allowing users to adjust configurations to optimize performance for their specific environment. <br/><br/>
 UIs were developed using WPF (Windows Presentation Foundation) and adhere to the MVVM (Model-View-ViewModel) design pattern.
### Challenges and Solutions
Mission Room installs display systems in a variety of environments, including head offices, site cabins, and event gazebos, each with unique lighting conditions from both artificial and natural sources. Touch is highly sensitive to light, as it relies on infrared light for its operation. Given the sensitivity to light and the necessity for optimal camera settings, manually adjusting exposure, gain, and gamma through the GUI proved impractical for users. To address this issue, I developed the "Auto Camera Brightness" feature. This solution initially sets all camera settings to their maximum values and then gradually reduces them. The process continues until the cameras no longer detect light, ensuring optimal camera settings for varying lighting conditions. <br/><br/>
Another challenge was light reflecting from one screen to the edge of adjacent screens. To address the issue of the mouse inadvertently moving between screens due to reflections, I developed the "Select Boundary" function. When the user taps a screen with the pen, that screen becomes active, allowing the pen to operate within its borders. If the user taps the edge of a non-active screen, the touch input is ignored, preventing unintended cursor movement between screens. <br/><br/>

### Role
When I first joined Mission Room, Touch was a minimal viable product created for a specific customer. My role was to independently redesign and redevelop the application. This was a significant challenge, especially as I had just finished university. I dedicated all my drive to developing Touch, focusing my learning exclusively on application development and applied all my previous knowledge of image processing to develop the core functionality of the application. As of now, Touch is utilized across all Mission Room systems for companies involved in infrastructure projects, as well as for training and onboarding purposes.

# Touch Camera Intrinsic Calibration application
### Overview
The Cameras that are used in the Touch application are mounted to the roof of the display systems. For Touch to work the cameras are required to view the entirety of the screens. Standard camera lenses often failed to capture the full screens, so fisheye lenses were adopted to ensure complete coverage. However, using fisheye lenses introduced visual distortion in the captured frames. For accurate calculation of the homography matrix in Touch, the frames need to be undistorted and appear flat. The application utilises OpenCV's fisheye undistort methods to correct the visual distortion in the images captured by the cameras, ensuring accurate, undistorted frames for Touch.
### Description
To undistort the camera frames, access to the cameras is required, which is achieved using the [Spinnaker SDK](https://www.flir.co.uk/products/spinnaker-sdk/?vertical=machine+vision&segment=iis). This SDK provides comprehensive access to all camera nodes and facilitates the retrieval of frames for processing. <br/><br/>
After setting up the camera and successfully retrieving frames, the next step is to correct image distortion. To undistort a fisheye image, a chessboard pattern is required. This pattern is widely used for camera calibration and lens distortion correction, as it offers a well-defined, regular pattern that can be easily detected and analyzed in images. <br/><br/>
Once a chessboard is detected using OpenCV, the frame is added to a list. After collecting 10 such images, the undistortion calculation is performed.<br/><br/>
The undistortion process returns two matrices, which are then stored in the cameras' onboard memory. When Touch starts up, it accesses this stored data to calculate the homography matrix for mouse movements.

# Touch Gestures
### Overview
One of the main limitations of Touch is that it supports only three interaction methods with the display system: moving the mouse, performing a left click, or executing a right click. This impedes users to get full control of the display system. I have developed a minimum viable product that introduces gesture controls to expand the interaction capabilities of Touch. The application operates by sending detected inputs from Touch, which are identified through blob detection, to the gesture application. This application then analyses the inputs and interprets them as specific gestures. I have implemented several gestures, including Undo, Redo, and Scroll Up and Down.
### Code
The code for this application can be found here [Gestures](https://github.com/DoddDev/Gestures)
### Description 

For communication between the Touch application and the Gesture application, named pipes were used. This allows Touch to pass mouse coordinates to the Gesture application in real time. When the Gesture application receives a new point from Touch, it is stored in a list. When the Gesture application receives a special "0,0,X" message from Touch, indicating the end of input, the list is processed.<br/>

The list of points is analyzed to determine if they form a straight line. The system checks for movements in four primary directions: up, down, left, and right. If a straight line is detected, a calculation is performed to determine the direction of the line, categorizing it into one of these four directions.<br/>

The processed directional data is then sent to the gesture class, where it is stored in a list of gestures. When retrieving a gesture, the list is concatenated into a string, representing the sequence of directional inputs, and passed to the GUI for user visualization. <br/>

Once the list contains three gestures (e.g., "up, up, down"), it is sent to the action manager. The action manager processes the sequence of gestures and compares it against a dictionary of predefined gestures and their associated actions. If the list matches a recognized gesture pattern, the corresponding action is executed.<br/>

Currently, the actions executed by the system are mapped to keyboard and mouse macros, like Ctrl-Z for undo or Ctrl-Y for redo. This allows the gesture-based system to mimic standard user inputs, providing an intuitive way for users to interact with the display system using gestures instead of traditional input methods.<br/>
The application features two distinct UIs: a debugging interface and a user-friendly interface. These can be switched by adjusting the settings in the XML configuration file.

- Debugging UI: This is primarily used for development and troubleshooting. It provides detailed visualizations of gestures, real-time data, and internal processes, helping developers ensure the accuracy of gesture recognition and input detection.
- User UI: Designed for simplicity, this interface displays only the current gesture and the last executed action. It offers a clean and intuitive experience for end users, keeping them informed about their interactions without overwhelming them with technical details. The minimal visualization helps users understand what's happening in real time, enhancing their experience while interacting with the system.<br/><br/>

Both UIs were developed using WPF (Windows Presentation Foundation) and adhere to the MVVM (Model-View-ViewModel) design pattern.# GSM-R Scenario
### Overview
This application was developed for East Midlands Railway as a training tool for train drivers to simulate the GSM-R emergency call process. It simulates trains moving across a line diagram and includes a full implementation of the UK railway signaling system. During the training session each driver is assigned to a train. The instructor can design real-world emergency scenarios for drivers to experience, allowing them to practice critical decision-making and response strategies in simulation.  When the instructor initiates the GSM-R emergency call, all trains in the simulation come to a stop. The instructor can also place a hazard on the line, and if a train encounters the hazard, the GSM-R emergency signal is automatically triggered. The drivers and the instructor then role play what to do in the given scenario. This simulation is highly beneficial for train drivers as it provides hands-on training in handling emergency situations. Drivers can practice and refine their responses in a controlled environment. This application helps ensure that drivers are well-prepared to manage emergencies effectively, enhancing overall safety on the railway.
### Description
This application consists of two key components: the railway infrastructure implementation and the visualisation implementation. The infrastructure component manages the train's starting position, determines the path it will take, and its final destination. It also establishes whether the train is moving up or down the line. Additionally, it manages the railway signals, including their positions and whether they are two, three, or four-aspect color-light signals. It also determines if each signal is for an up or down line. Each time a train moves, the system identifies the next closest signal on the line and calculates the distance to it. If the train enters the bounding box of the signal, it reacts according to the signal's color:

- Red: The train stops immediately.
- Yellow or Double Yellow: The train begins to slow down.
- Green: The train continues at its normal speed.<br/>

This models how the train network works. The signals also have interactive functionality. When the instructor clicks on a signal, it can be changed from green to red. If a signal is set to red, the first signal behind it turns to double yellow, and the one before that turns to yellow. This feature allows the trainer to create varied scenarios, as trains can be in different positions when the emergency is triggered.<br/><br/> The hazard implementation provides a drop box of different hazards that the instructor can place on the track. To allow for further role playing.<br/>

To manage the location of trains and signals, I developed an auxiliary application that enables instructors to import different line maps. This tool allows them to define train routes and place signals, accurately reflecting real-life tracks. All this is saved as an XML file and its stored in a file path that the main application can access.<br/>

The visualization implementation allows instructors to load various scenarios with different line maps and train configurations. When a scenario is loaded, the map occupies the entire display system, accommodating large training groups. The visualization displays the movement of trains and all signal statuses. When an emergency is triggered, all signals turn red and trains are halted. The instructor can then interact with each driver, asking questions such as, "Is it safe for your train to continue to the next signal?"

### Role
My role in this application was my first experience leading a customer-focused project. It began with an introductory meeting where I learned about the GSM-R emergency call and its use in the train network. I then researched the topic and developed a prototype. After several meetings and adjustments based on feedback, I delivered the final application and observed a training session conducted by the instructors. From these observations, I gathered feedback and made further enhancements to the application to better support their training needs. This project provided me with invaluable experience in working directly with customers and transforming their needs into a functional application. Through interactions with the customer, I gained a deeper understanding of their requirements and the specific challenges they faced. By actively listening to their feedback and incorporating it into the development process, I learned how to align technical solutions with their operational needs. This experience enhanced my ability to manage client relationships, address their concerns effectively, and deliver solutions that are both practical and impactful. 

