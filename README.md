# Scene To CAD

## Source

```bash
https://github.com/hmz-15/Interactive-Scene-Reconstruction
```

## Prepare Environment

Better choise: Do not use Anaconda

```bash
pip3 install torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cu113
python -m pip install detectron2 -f \
  https://dl.fbaipublicfiles.com/detectron2/wheels/cu113/torch1.10/index.html
sudo apt remove libgoogle-glog-dev
sudo apt autoremove
cd github
git clone https://github.com/google/glog.git
cd glog
mkdir build && cd build
cmake ..
sudo make install -j
```

## Build

```bash
mkdir -p scene2cad_ws/src
cd scene2cad_ws
catkin init
catkin config --extend /opt/ros/noetic --merge-devel 
catkin config --cmake-args -DCMAKE_CXX_STANDARD=14 -DCMAKE_BUILD_TYPE=Release -DCMAKE_EXPORT_COMPILE_COMMANDS=Yes
cd src
git clone --recursive https://github.com/hmz-15/Interactive-Scene-Reconstruction.git
cd ..
sh src/Interactive-Scene-Reconstruction/wstool_setup_https.sh
cd src/Interactive-Scene-Reconstruction
pip3 install -r requirements.txt
pip3 install opencv-python-headless==4.1.2.30
cd mapping/rp_server
make dev -j
cd ../../../..
catkin build panoptic_mapping_pipeline map_proc scene_builder gazebo_simulation
```

## Download

```bash
https://drive.google.com/file/d/1_4-gcVnPx9S-woaQVBHSTEpW7bX4yRNW/view?usp=sharing
->
~/.ros/scenenn/scenenn_255.bag

https://drive.google.com/file/d/1cQ_bwdK7-jyIUMsteClNdiXIZWrYT5cX/view?usp=sharing
->
scene2cad_ws/src/Interactive-Scene-Reconstruction/

https://drive.google.com/file/d/1P2fgpqfWpkhg-CFKS3YpXGP70aKf9tTe/view?usp=sharing
->
scene2cad_ws/src/Interactive-Scene-Reconstruction/output/
```

make sure it looks like

```bash
scene2cad_ws/src/Interactive-Scene-Reconstruction/
  cad_dataset/
    rigid_object/
      xxx.obj
      xxx.mtl
      xxx.png
      ...
    articulated_object/
      fridge_0001/
        xxx.obj
        xxx.mtl
        xxx.urdf
        xxx.xacro
        ...
      fridge_0002/
      ...
    gazebo_world/
      ...
```

```bash
scene2cad_ws/src/Interactive-Scene-Reconstruction/output/
  sceneNN_test/
    contact_graph/
      contact_graph_cad.json
      ...
    panoptic_segments/
      1.ply
      ...
```

and run

```bash
source devel/setup.zsh
roslaunch scene_builder generate_xacro_scene.launch scene_name:=sceneNN_test enable_physics:=true enable_gazebo:=true
roslaunch scene_builder generate_gazebo_world.launch scene_name:=sceneNN_test
```

NOTE: when it shows "[OK]", use Ctrl+C to kill the process.

## Run

### Scene Visualization

```bash
roslaunch scene_builder view_scene.launch scene:=sceneNN_test
```

### Launch the map processing node

```bash
roslaunch map_proc map_processing.launch sequence:=sceneNN_test
```

### Visualize contact graph

```bash
cd src/Interactive-Scene-Reconstruction
python cad_replacement/pg_map_ros/pg_map_ros/pg_viewer/launch_viewer.py -c output/sceneNN_test/contact_graph/contact_graph_cad.json
cd ../..
```

### Load Gazebo

```bash
roslaunch gazebo_simulation gazebo_world.launch scene_name:="sceneNN_test" enable_robot:="false"
```

### Run Mapping on SceneNN Dataset

```bash
python src/Interactive-Scene-Reconstruction/mapping/rp_server/launch_detectron_server.py
```

```bash
roslaunch panoptic_mapping_pipeline scenenn_pano_mapping.launch sequence:=sceneNN_test
```

## Enjoy it~

