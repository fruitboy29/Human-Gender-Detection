# Human-Gender-Detection
This project utilizes AI to detect the gender of people as well as categorize them by children or adults. The AI detects 4 classes: boys, girls, men, and women. The reason I created this AI is to create a baseline for a future project in which students at preschools can be ensured to having at least one supervising adult watching them while they use the playground. As my parents own a preschool, this can be applied to their school to reduce risk of students getting injured when unsupervised.
# The Algorithm
This program uses detectnet and restnet-18 for this project. First, I used the image database from https://storage.googleapis.com/openimages/web/visualizer/index.html?set=train&type=detection&c=%2Fm%2F014j1m to download on my Jetson Orin. To do this, I went to my jetson-inference directory by using "cd ~/jetson-inference/" in the terminal, then I went to the docker using "./docker/run.sh" in the terminal as well. From there, I navigated into the detection ssd folder by running "cd python/training/detection/ssd" in the docker. To download the specific images used for this project I ran this in the detection ssd section of the docker: 

python3 open_images_downloader.py --max-images=5000 \
--class-names "Girl,Boy,Man,Woman" \
--data=data/person

Once the images had downloaded I trained my model using the code:

python3 train_ssd.py --data=data/person --model-dir=models/person --batch-size=4 --epochs=50

*For this code, epochs is the amount of times that the program analyzes each picture of the dataset. Note that it is important to stay consistent using /person to be in the right directory throughout the project.*

# Running This Project
To run this AI detection of humans, few steps must be completed:
Before doing this, navigate into the docker through using following code -
1. cd ~/jetson-inference/
2. ./docker/run.sh
3. cd python/training/detection/ssd

1. Find and download a video that includes people that can be recognized as a girl, boy, man, or woman as an mp4.
2. *Optional* Rename the video to something easy to remember and type.
3. Drag and drop the downloaded video into the data directory on the left hand side with all the directories under jetson-inference/python/training/detection/ssd/data.
4. Convert the trained model from PyTorch to ONNX so it can be loaded with TensorRT by using the following code in the docker: python3 onnx_export.py --model-dir=models/person
5. Use detectnet to load our custom SSD-Mobilenet ONNX model by using the following code in the docker: detectnet --model=models/person/ssd-mobilenet.onnx --labels=models/person/labels.txt \
          --input-blob=input_0 --output-cvg=scores --output-bbox=boxes \
           data/[yourvideoname.mp4] data/[yournameOutput.mp4]
*For [yourvideoname.mp4] and [yournameOutput.mp4], make sure the videoname typed is the one downloaded which is made easier if you renamed it earlier. The output can be called whatever you want it to be.

*Note there is a demo video in the demo folder of the github project*
