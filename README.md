# Distributed Image Processing - GCP Dataproc
## Spinning up Apache Spark clusters for Image Feature Detection
---
## 1. Create VM Instance

-Create a VM in Google Cloud Platform

> Compute Engine -> VM Instances -> Create

-Make sure it has at least 3 nodes (i.e. n1-standard-2 instance)

> Debian GNU/Linux 9 Image

-Allow full access to all Cloud APIs

-SSH into Instance

## 2. Install Scala and sbt

```
sudo apt-get install -y dirmngr
```
```
sudo apt-get update
```
```
sudo apt-get install -y apt-transport-https
```

```
echo "deb https://dl.bintray.com/sbt/debian /" | \
sudo tee -a /etc/apt/sources.list.d/sbt.list
```

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 642AC823

```


```
sudo apt-get install -y bc scala sbt
```
## 3. Set up Feature Detection Files

```
sudo apt-get update
```


```
git clone https://github.com/GoogleCloudPlatform/cloud-dataproc
```



```
cd cloud-dataproc/codelabs/opencv-haarcascade
```

## 4. Launch Build


```
sbt assembly
```
## 5. Create a Storage Bucket

```
gsutil mb gs://${MYBUCKET}
```
### Download some images into bucket


```
curl https://www.publicdomainpictures.net/pictures/20000/velka/parents-holding-baby-871294937167Xx4.jpg | gsutil cp - gs://${MYBUCKET}/imgs/green-bananas.jpg
```

```
curl https://www.publicdomainpictures.net/pictures/20000/velka/family-of-three-871290963799xUk.jpg | gsutil cp - gs://${MYBUCKET}/imgs/family-of-three.jpg
```

```
curl https://www.publicdomainpictures.net/pictures/10000/velka/296-1246658839vCW7.jpg | gsutil cp - gs://${MYBUCKET}/imgs/classroom.jpg
```

###See Contents of Bucket
```
gsutil ls -R gs://${MYBUCKET}
```
## 6. Create a Cloud Dataproc Cluster


```
MYCLUSTER="${USER/_/-}-qwiklab"
```

```
echo MYCLUSTER=${MYCLUSTER}
```

>Set Global Region

```
gcloud config set dataproc/region global
```


```
gcloud dataproc clusters create ${MYCLUSTER} --bucket=${MYBUCKET} --worker-machine-type=n1-standard-2 --master-machine-type=n1-standard-2   
```

>If prompted use zone instead of region.
## 7. Submit Job to Dataproc

>Run in SSH to load face detection config file in your bucket


```
curl https://raw.githubusercontent.com/opencv/opencv/master/data/haarcascades/haarcascade_frontalface_default.xml | gsutil cp - gs://${MYBUCKET}/haarcascade_frontalface_default.xml
```

>Submit job with your uploaded images


```
cd ~/cloud-dataproc/codelabs/opencv-haarcascade
```



```
gcloud dataproc jobs submit spark \
--cluster ${MYCLUSTER} \
--jar target/scala-2.10/feature_detector-assembly-1.0.jar -- \
gs://${MYBUCKET}/haarcascade_frontalface_default.xml \
gs://${MYBUCKET}/imgs/ \
gs://${MYBUCKET}/out/
```

### Monitor the Job in the Console
> Navigation menu -> Dataproc -> Jobs

### When the job is complete goto Bucket and click on an image in the 'Out' directory

> Navigation menu -> Storage


---


### For Better Results- Try out the Google Vision API

---






































