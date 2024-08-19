
# Launching Application on Amazon EC2 




## Task
1. Launch one Small size ( t2.micro to t2.small ) Amazon Linux   2023 x86 AMI Instance.
2. Attach one 4-8GB EBS volume and mount /mnt/data
3. Understand dependency and Install Below Application.

    Application Name : File browser  
    Website: https://filebrowser.org/installation  
    Github: https://github.com/filebrowser/filebrowser
4. Use /mnt/data path as a storage directory in Filebrowser.
5. Launch application in the background on Port 80.


##

Launch an Amazon EC2 Instance:

Launch a t2.micro or t2.small Amazon Linux 2023 x86 AMI instance.
Attach and Mount an EBS Volume:

Attach a 4-8GB EBS volume to the instance.
Mount the volume to /mnt/data.
Install Filebrowser:

Install the necessary dependencies.
Download and configure Filebrowser.
Configure and Run Filebrowser:

Configure Filebrowser to use /mnt/data as the storage directory.
Launch Filebrowser in the background on port 80.

## Solution/Description
1. Filebrowser: It provides a file managing interface within a specified directory and it can be used to upload, delete, preview,
rename, and edit your files. It allows the creation of multiple users and each user can have its own directory.

2. Amazon EC2: Amazon Elastic Compute Cloud (Amazon EC2) offers the broadest and deepest compute platform, with over 750 instances and choice of the latest processor, storage, networking, operating system, and purchase model to help you best match the needs of your workload.

3. Amazon Elastic Block Store (Amazon EBS) is an easy-to-use, scalable, high-performance block-storage service designed for Amazon Elastic Compute Cloud (Amazon EC2). Close.

## Solution
1. Launch an Amazon EC2 instance
➔ Go to EC2 dashboard
➔ Launch instance
➔ Choose the Amazon Linux 2023 AMI
➔ Choose t2.micro
➔ Create EC2
![alt text](<Ec2.jpg>)

-> Click on Security

->	Edit Inbound rule

->Configure HTTP (port 80) and SSH (port 22) and open of internet.

![alt text](<sec.jpg>)


-> Under ELASTIC BLOCK STORAGE - click on Volumes

-> Create a volume of 8gb and attach to your instance(EC2).

![alt text](<volume.jpg>)

->Now ec2 and volume created.

->We have to mount it to mnt/data

2. 	Go to EC2 instance and click on connect and open command prompt.


 -> Become root user by – sudo su –

 -> Sudo yum update – to just check you command prompt is update.

 ->   Now make a directory -  mkdir /mnt/data

 ->  Directory create so we have to mount our current volume to   above path.

 -> Run these commands

    sudo mkfs -t ext4 /dev/xvdf
    sudo mkdir /mnt/data
    sudo mount /dev/xvdf /mnt/data
    sudo chmod 777 /mnt/data

->	Now your volume is mount on /mnt/data.

![alt text](<cmd1.jpg>)


3.	Now we will install Filebrowser on our server

    ->	Make a temp folder/directory and go inside it.

    Mkdir temp

    Cd temp
  ->	Now click on link provided in problem statement -Website: https://filebrowser.org/installation

->	You will direct to installation page of filebrowser

->	Now under quick set up click on Unix

->	Now copy the code that will be –
 curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash
filebrowser -r /path/to/your/files

but here we will change little in code – after pipline command that is 
bash  filebrowser -r /path/to/your/files  
we will change path with our mnt/data  path .

->final command - curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash filebrowser -r /mnt/data

run this command

![alt text](<cmd2.jpg>)

4.	After running above command we see filebrowser listening on 8080 port not 80 port.
So we will configure it.

We will come out from temp folder and as root user we will run below script.

current_user=$(whoami)
current_group=$(id -gn)

sudo tee /etc/systemd/system/filebrowser.service > /dev/null <<EOF
[Unit]
Description=Filebrowser
After=network.target

[Service]
ExecStart=/usr/local/bin/filebrowser -r /mnt/data --address 0.0.0.0 --port 80 --database /mnt/data/filebrowser.db
Restart=always
User=$current_user
Group=$current_group
Environment=PATH=/usr/local/bin:/usr/bin:/bin
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target
EOF

    sudo systemctl daemon-reload
    sudo systemctl start filebrowser
    sudo systemctl status filebrowser


![alt text](<cmd3.jpg>)

-> After running above command it will directed http connection to port 80.

![alt text](<cmd4.jpg>)


->	All done here we successfully host our application on EC2 instance at port 80

5.	Now go to your EC2 instance and click on your public ip address(link).

->	It will open a new tab in your browser with your ec2 instance public ip.

->	You can see filebrowser application.

NOTE -> click on your EC2 instance link and edit -> https -> to -> http in your web browser.


![alt text](<app_host.jpg>)

                            DONE

                           Thankyou


