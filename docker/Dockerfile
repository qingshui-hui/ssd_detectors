FROM tensorflow/tensorflow:2.6.0-jupyter

RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y libgl1-mesa-dev

RUN pip install scipy pandas opencv-python==4.5.2.54 tqdm==4.61.1 imageio==2.9.0 editdistance pyclipper

# https://github.com/git-lfs/git-lfs/blob/main/INSTALLING.md
RUN curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | bash
RUN apt-get install git-lfs

# https://gist.github.com/33eyes/431e3d432f73371509d176d0dfb95b6e
git config filter.strip-notebook-output.clean 'jupyter nbconvert --ClearOutputPreprocessor.enabled=True --to=notebook --stdin --stdout --log-level=ERROR'