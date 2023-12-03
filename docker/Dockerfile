FROM tensorflow/tensorflow:2.6.0-jupyter

RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y libgl1-mesa-dev

RUN pip install opencv-python==4.5.2.54 tqdm==4.61.1 imageio==2.9.0 editdistance pyclipper
