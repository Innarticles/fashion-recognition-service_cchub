# fashion-recognition-service_cchub
A simple image recognition agent for fashion items. 

- Create images folders and upload to s3
#Done
- Login to instance
#Done

## 1. Downoload the images and unzip
$ curl -O https://s3.amazonaws.com/onomachinelearning/fashion_items.tar.gz
$ tar xzf fashion_items.tar.gz 

## 2. Run the following to retrain the InceptionV3 model with your images
$ vi tensorflow/workspace.bzl 
$ bazel build tensorflow/examples/image_retraining:retrain
$ bazel-bin/tensorflow/examples/image_retraining/retrain --image_dir ~/fashion_items

## 3. Test that the model is working
$ bazel-bin/tensorflow/examples/label_image/label_image --graph=/tmp/output_graph.pb --labels=/tmp/output_labels.txt -- output_layer=final_result --image=$HOME/fashion_items/shoes/zzhumb-300-002.jpg

## 4. Copy the model to another permanent folder
$ mkrdir mymodel
$ cd ~
$ mkdir mymodel
$ cd /tmp 
$ cp {output_graph.pb, output_label.txt} ~/mymodel

## 5. Making sure it still works
$ bazel-bin/tensorflow/examples/label_image/label_image --graph=$HOME/mytmp/output_graph.pb --labels=$HOME/mytmp/output_labels.txt --output_layer=final_result --image=$HOME/fashion_items/shoes/zzhumb-300-002.jpg


## 6. Install flask
http://www.datasciencebytes.com/bytes/2015/02/24/running-a-flask-app-on-aws-ec2/

$ sudo apt-get update
$ sudo apt-get install apache2
$ sudo apt-get install libapache2-mod-wsgi
$ sudo pip install flask
$ mkdir ~/flaskapp
$ sudo ln -sT ~/flaskapp /var/www/html/flaskapp

$ touch flaskapp.wsgi
$ vi flaskapp.wsgi
$ touch /etc/apache2/sites-enabled/000-default.conf
$ sudo vi /etc/apache2/sites-enabled/000-default.conf
$ echo "Hello World" > index.html
$ touch flaskapp.py
$ vi flaskapp.py
$ cp ~/fashion-image-recongizer/flaskapp.py .
$ cp /home/ubuntu/mytmp/output_graph.pb .
$ cp /home/ubuntu/mytmp/output_labels.txt .


$ sudo vi /var/log/apache2/error.log
$ sudo apachectl restart
