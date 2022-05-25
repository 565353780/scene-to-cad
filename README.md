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
https://drive.google.com/file/d/1cQ_bwdK7-jyIUMsteClNdiXIZWrYT5cX/view?usp=sharing
->
scene2cad_ws/src/Interactive-Scene-Reconstruction/cad_dataset/

https://drive.google.com/file/d/1P2fgpqfWpkhg-CFKS3YpXGP70aKf9tTe/view?usp=sharing
->
scene2cad_ws/src/Interactive-Scene-Reconstruction/output/
```

make sure it looks like

```bash
output/
  sceneNN_*/
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
roslaunch scene_builder generate_xacro_scene.launch scene_name:=sceneNN_test
```

## Run

```bash

```

## Enjoy it~

