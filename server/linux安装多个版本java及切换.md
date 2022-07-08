# linux安装多个版本java及切换

In order to install java 11:

sudo amazon-linux-extras install java-openjdk11

For java 8 you can try:

sudo yum install java-1.8.0-openjdk

Finally, if you want to switch between java versions run:

sudo alternatives --config java

[AWS - How to install java11 on an EC2 Linux machine?](https://stackoverflow.com/questions/59430965/aws-how-to-install-java11-on-an-ec2-linux-machine)
