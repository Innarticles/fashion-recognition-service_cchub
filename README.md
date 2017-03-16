## fashion-recognition-service_cchub
A simple image recognition agent for fashion items. 

#### 1. Create images folders and upload to s3

#### 2. Downoload the images and unzip
```
$ curl -O https://s3.amazonaws.com/onomachinelearning/fashion_items.tar.gz
$ tar xzf fashion_items.tar.gz 
```

#### 3. Run the following to retrain the InceptionV3 model with your images
```
$ vi tensorflow/workspace.bzl 
$ bazel build tensorflow/examples/image_retraining:retrain
$ bazel-bin/tensorflow/examples/image_retraining/retrain --image_dir ~/fashion_items
```

#### 4. Test that the model is working
```
$ bazel-bin/tensorflow/examples/label_image/label_image --graph=/tmp/output_graph.pb --labels=/tmp/output_labels.txt -- output_layer=final_result --image=$HOME/fashion_items/shoes/zzhumb-300-002.jpg
```

#### 5. Copy the model to another permanent folder
```
$ mkrdir mymodel
$ cd ~
$ mkdir mymodel
$ cd /tmp 
$ cp {output_graph.pb, output_label.txt} ~/mymodel
```

#### 6. Making sure it still works
```
$ bazel-bin/tensorflow/examples/label_image/label_image --graph=$HOME/mytmp/output_graph.pb --labels=$HOME/mytmp/output_labels.txt --output_layer=final_result --image=$HOME/fashion_items/shoes/zzhumb-300-002.jpg
```


#### 7. Install flask
http://www.datasciencebytes.com/bytes/2015/02/24/running-a-flask-app-on-aws-ec2/
```
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
```
#### 8. Copy models to app folder
```
$ cd ~/flaskapp
$ cp ~/fashion-image-recongizer/flaskapp.py .
$ cp /home/ubuntu/mytmp/output_graph.pb .
$ cp /home/ubuntu/mytmp/output_labels.txt .
```
#### 9. App code
https://gist.github.com/Innarticles/3a4a2f4797e44a53f2143970c65de056#file-flaskapp-py"

- main function /classify
```python
# Classificaiton endpoint
@app.route("/classify", methods=["POST"])
def classify():

  create_graph()
  print("Model loaded")

  node_lookup = NodeLookup()
  print("Node lookup loaded")


  predictions = dict(run_inference_on_image(request.data))
  print(predictions)
  return jsonify(predictions=predictions)
```



#### 10. Useful commands
See app logs
```
$ sudo vi /var/log/apache2/error.log
```
Restart server
```
$ sudo apachectl restart
```

#### 11. Useful resources
1. Dive straight in https://gist.github.com/Innarticles/3a4a2f4797e44a53f2143970c65de056#file-flaskapp-py
2. Best resource for starting up https://github.com/asseldonk/tensorflow-jupyter-tutorial
3. Installing Tensorflow on GPU https://medium.com/@giltamari/tensorflow-getting-started-gpu-installation-on-ec2-9b9915d95d6f#.rxse36e0i
4. Installing flask http://www.datasciencebytes.com/bytes/2015/02/24/running-a-flask-app-on-aws-ec2/
