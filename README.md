# Image-labels-generator-using-Amazon-Rekognition

## Overview of Project ☁️
In this project, we will be building an image labels generator, using Amazon Rekognition. This is going to be a fun one. Once built, it will be able to recognize and label images. For example, if you have a photo of a cat, Amazon Recognition will be able to identify what it is, and label the image as a cat.

**Steps to be performed 👩‍💻**
In the next few lessons, we'll be going through the following steps.

**Creating an Amazon S3 Bucket**

- Uploading images to the S3 Bucket
- Installing configuring the AWS Command line interface (CLI)
- Importing libraries
- Adding detect_labels function
- Adding main function
- Running your python file

**Services Used 🛠**

- Amazon S3: For storing the images in the process of generating labels.
- Amazon Rekognition: To analyse images and generate image labels.
- AWS CLI: Interacting with AWS services through command line interface(CLI).

**Estimated Time & Cost ⚙️**
This project is estimated to take about 20-30 minutes
Cost: Free (When using the AWS Free Tier)

**➡️ Architectural Diagram**
This it the architectural diagram I created for the project:
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/aghh856v8ocj93hkdgkl.png)


**➡️ Final Result**
This is what your project will look like, once built:
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7tlfw28qwtsjcz7p5nko.png)





## Create S3 bucket and upload images

1.Log in to your **AWS Management Console**.

2.Navigate to the **Amazon S3** service from the search bar. 

An S3 bucket is like a virtual storage box in the cloud where you can keep your files safe and easily accessible with permissions.
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/eorrk7qczo2b7541bax6.png)

3.Click on "**Create Bucket**"

4.Choose a unique name for your bucket and select the region the want the storage bucket.
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bzsz4p42h9edzmsj7con.png)


5.Leave the default settings for the rest of the options and click "**Create Bucket**".

6.We will use this bucket to store the images on which labels are to be generated. Let’s go ahead and upload some images in the S3 bucket.



## Upload Images to S3 Bucket
1.Once the bucket is created, navigate to the bucket.

2.Click on the "**Upload**" button and select the images you want to analyze from your system.
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h2td7bb0l85lebmfbxe4.png)

In this case, you may download and use this image (_image1.png_) for this documentation:
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qq8rnyouq5d9sy6vez97.jpg)

And once you mastered how to use this service, you may experiment other images!

3.Scroll down and then Click on "**Upload**". Your image has now been uploaded in the S3 bucket.
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6smlv2iapsuses2bxer2.png)

We will use these images for labelling so try to upload some images that have multiple objects present to see the accuracy of the model when multiple labels are present. In this example we use a busy city street image.

## Installing the AWS Command line interface(CLI)
The AWS CLI is used to interact with various AWS services from the command line. To install AWS CLI:

1.Open your terminal or command prompt.

2.Run the command appropriate for your operating system to install the AWS CLI.


_**TEXT**_
```
#For Windows:
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi

#For macOS (using Homebrew):
brew install awscli

#For Linux (using package manager):
sudo apt-get install awscli

```

To check if the AWS CLI (Command Line Interface) was installed successfully on your system, you can run the following command in your terminal or command prompt:

`aws --version`

Note: Restart your terminal if it is giving not installed error

You have successfully installed the CLI. Next, we need to configure the CLI with the appropriate user keys to get started with using AWS services.

**Configure AWS CLI**
1.To configure your AWS CLI, run the following command in your terminal:

`aws configure`

2.Running this command will ask for a access key and secret access key.
What are these?

To access your AWS account from CLI, you need to set up a user account associated with it and these keys are used for the authentication for accessing the AWS services.

3.Login to your **AWS Management console** and search for **IAM** in the search bar.

4.Navigate to **Users** and click on **Create User**.

5.Give an appropriate user name and **Next**.
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zmtxl2d7kbn0z5qt3k93.png)



6.For the Permission options, choose the option **Attach policies directly** and attach the "**AdministratorAccess policy**".

(Be careful while using the Administrator Access policy as we get the full access to the AWS services and is generally not recommended if the user will be accessed by someone else). 

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cgkmoemkruquotac61u4.png)

7.Click on Next and Create User.

8.Navigate to the user you created, and click on **Create Access key** under the Access Keys option.

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3b2njjyf29hwoy4a14ua.png)


9.Choose the **Command Line Interface(CLI) **as the use case, check the confirmation box and click **Next**.
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/53jyeuzna5scs4wo410a.png)

10.Provide a suitable description about purpose of the Access key and Create Access Key.

11.You will be provided with an Access key and a Secret Access Key. These keys can be used to access your AWS services so make sure these are kept confidential.

12.Some best practices while using Access Keys:

- Never store your access key in plain text, in a code repository, or in code.
- Disable or delete access key when no longer needed.
- Enable least-privilege permissions.
- Rotate access keys regularly.

13.Go back to your terminal or command prompt and paste the keys that you just generated.

14.Choose the region (Make sure the CLI default region and the S3 bucket region are the same)

15.The general flow of the command line would look like:

**_TEXT_**
```
PS H:\AWS-beginner-friendly-projects\Image label> aws configure
AWS Access Key ID [****************L4TV]: ****************
AWS Secret Access Key [****************mtjR]: *******************
Default region name [us-east-1]: us-east-1
Default output format [None]: None
```

We have configured our AWS CLI. For our next steps, we will write the python code for extracting pictures from S3 bucket and applying detect_labels operation from Rekognition to generate the labels with their confidence score. 



## Import Libraries
Open your preferred IDE(VSCode etc.) to and create a .py file for performing your coding.

Open a terminal and install the libraries needed for this project.
```
pip install boto3
pip install matplotlib
```

Let's import the necessary libraries. We need:

- boto3 for interacting with AWS services.
- matplotlib for visualization.
- PIL (Python Imaging Library) for handling image data.
- BytesIO from the io module to work with image data.


Add the below code in your .py file.
**_python_**

```
import boto3
import matplotlib.pyplot as plt
import matplotlib.patches as patches
from PIL import Image
from io import BytesIO
```

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6ows3ihqpph4z105iemo.png)


## detect_labels function
Now, let's define a function called detect_labels. This function takes a photo and bucket name as input parameters. Within the function:

- We create a **Rekognition **client using boto3.
- We use the **detect_labels** method of the Rekognition client to detect labels in the given photo.
- We print the detected labels along with their confidence levels.
- We load the image from the **S3 bucket** using boto3 and PIL.
- We use **matplotlib** to display the image and draw bounding boxes around the detected objects.

**_python_**

```
def detect_labels(photo, bucket):
    # Create a Rekognition client
    client = boto3.client('rekognition')

    # Detect labels in the photo
    response = client.detect_labels(
        Image={'S3Object': {'Bucket': bucket, 'Name': photo}},
        MaxLabels=10)

    # Print detected labels
    print('Detected labels for ' + photo)
    print()
    for label in response['Labels']:
        print("Label:", label['Name'])
        print("Confidence:", label['Confidence'])
        print()

    # Load the image from S3
    s3 = boto3.resource('s3')
    obj = s3.Object(bucket, photo)
    img_data = obj.get()['Body'].read()
    img = Image.open(BytesIO(img_data))

    # Display the image with bounding boxes
    plt.imshow(img)
    ax = plt.gca()
    for label in response['Labels']:
        for instance in label.get('Instances', []):
            bbox = instance['BoundingBox']
            left = bbox['Left'] * img.width
            top = bbox['Top'] * img.height
            width = bbox['Width'] * img.width
            height = bbox['Height'] * img.height
            rect = patches.Rectangle((left, top), width, height, linewidth=1, edgecolor='r', facecolor='none')
            ax.add_patch(rect)
            label_text = label['Name'] + ' (' + str(round(label['Confidence'], 2)) + '%)'
            plt.text(left, top - 2, label_text, color='r', fontsize=8, bbox=dict(facecolor='white', alpha=0.7))
    plt.show()

    return len(response['Labels'])
```

**main function**
Next, let's write a main function to test our detect_labels function. We specify a sample photo and bucket name, then call the detect_labels function with these parameters.

(Remember to change your ‘`image_file_name`’ and ‘`bucket_name`’ to your actual configured naming.)

**_python_**

```
def main():
    photo = 'image_file_name'
    bucket = 'bucket_name'
    label_count = detect_labels(photo, bucket)
    print("Labels detected:", label_count)

if __name__ == "__main__":
    main()
```


## Running your Python file
1.Open the terminal in the directory where your Python file is present and run the command:

`python name_of_python_file.py`

You will get an output with 10 detected labels and their confidence levels and a pop-up screen displaying the image that was uploaded on your S3 bucket with the bounding boxes present on the generated labels. 

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6ma4zl72nr92lr50ykfj.png)


![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jrydkm26g2lnu4j1f6sy.png)


## Conclusion
Congratulations on completing the Image Label Generator project using AWS Rekognition. **You've done an excellent job.**

If you enjoyed working on this project and want to explore more functionalities or ideas to expand it further, here are some recommendations for you:

**1.Adding Video Label Detection:**

- Extend your project to support video label detection. You can use AWS Rekognition's video analysis capabilities to analyse videos and detect labels in each frame.
- This could be useful for applications like video content moderation or surveillance

**2.Adding Real-Time Object Label Detection:**

- Enhance your project to support real-time object label detection using live camera feeds. You can use libraries like OpenCV to capture video frames from a webcam and then use AWS Rekognition to detect objects in real-time.

**3.Analysing Facial Expressions:**

- Explore AWS Rekognition's facial analysis capabilities to analyse facial expressions in images or videos.
- You can detect emotions like happiness, sadness, anger, etc., and use this information for various applications such as sentiment analysis, user feedback analysis, or personalized marketing.

**4.Identifying Faces:**

- Expand your project to include face recognition capabilities.
- AWS Rekognition offers face recognition APIs that allow you to detect and recognize faces in images or videos.
- You can use this feature for applications like user authentication, access control, or personalized recommendations.
- Have fun experimenting and building new features for your project

**Clean-up**
Login to the **AWS Management Console** and go to **S3** using the search bar. Select the **S3** bucket created for this project and choose the **Delete** option located in the top right corner of the general-purpose buckets section.

Next, navigate to **Lambda** from the search bar. Select the Lambda function created for this project and go to **Actions → Delete** option available in the top right corner.

Lastly, navigate to **IAM** from the search bar. Select the User created to grant **CLI** access and choose the **Delete** option provided in the top right corner.

I'm proud of you!
