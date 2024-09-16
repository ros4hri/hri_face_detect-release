# hri_face_detect

A [ROS4HRI](https://wiki.ros.org/hri)-compiant ROS node to perform fast face
detection using
[YuNet face detector](https://github.com/ShiqiYu/libfacedetection) and
[Mediapipe Face Mesh](https://github.com/google/mediapipe/blob/master/docs/solutions/face_mesh.md).
The former performs well at greater distances (depending on image resolution
and image scaling applied) and extracts 5 keypoints.
The latter works only at close distances and extracts all the ROS4HRI-defined
landmarks.

## ROS API

### Parameters

All parameters are loaded in the lifecycle `configuration` transition.

- `processing_rate` (int, default: 30):
  Image processing logic execution rate in Hertz.

- `face_mesh` (bool, default: true):
  It enables the additional Mediapipe Face Mesh detection.

- `confidence_threshold` (double, default: 0.75):
  Candidate face detections with confidence lower that this threshold are not
  published.

- `image_scale` (double, default: 0.5):
  The YuNet face detector accepts input image of dynamic size.
  This parameter controls the rescale factor applied to the input image before running the YuNet face detector.
  Lower image scale results in less processing time required and lower detection
  confidences.
  The output data (e.g., RoI) is invariant with this parameter and always refers
  to the original input image size.

- `filtering_frame` (string, default: "camera_color_optical_frame"):
  The reference frame the estimated face pose should be transformed to before
  performing the filtering operations.
  Due to the proximity between the camera frame and the detected faces, and
  considering that cameras can be mounted on frequently moving robot's
  components (e.g., robot's head), directly filtering a face pose expressed in 
  camera optical frame might reduce the filtering quality.

- `deterministic_ids` (bool, default: false):
  If true the face ids start from "f00000" and increases by one for each new
  face. If false it is a random five letters sequence.

- `debug` (bool, default: false):
  If true opens a windows showing the input image with face detections
  overlayed.

### Topics

This package follows the ROS4HRI conventions ([REP-155](https://www.ros.org/reps/rep-0155.html)).
If the topic message type is not indicated, the ROS4HRI convention is implied.

#### Subscribed

- `image_raw` ([sensor_msgs/msg/Image](https://github.com/ros2/common_interfaces/blob/humble/sensor_msgs/msg/Image.msg))
- `camera_info` ([sensor_msgs/msg/CameraInfo](https://github.com/ros2/common_interfaces/blob/humble/sensor_msgs/msg/CameraInfo.msg))

#### Published

- `/humans/faces/<faceID>/roi`
- `/humans/faces/<faceID>/landmarks`
- `/humans/faces/<faceID>/cropped`
- `/humans/faces/<faceID>/aligned`
- `/humans/faces/tracked`
- `/diagnostics` ([diagnostic_msgs/msg/DiagnosticArray](https://github.com/ros2/common_interfaces/blob/humble/diagnostic_msgs/msg/DiagnosticArray.msg))

## Execution

```bash
ros2 launch hri_face_detect face_detect.launch rgb_camera:=<input camera namespace>
```

## Example

For an example of usage, execute in different terminals:
- USB camera:
  1. `apt install ros-humble-usb-cam`
  2. `ros2 run usb_cam usb_cam_node_exe`
- HRI face detect:
  1. `apt install ros-humble-hri-face-detect`
  2. `ros2 launch hri_face_detect face_detect.launch.py`
- RViz with HRI plugin:
  1. `apt install ros-humble-rviz2`
  1. `apt install ros-humble-hri-rviz`
  2. `rviz2`

In RViz, add the 'Humans' plugin to see the detected faces with the relative keypoints.
