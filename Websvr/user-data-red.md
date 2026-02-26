#!/bin/bash
yum update -y
yum install -y httpd
# start httpd daemon
systemctl start httpd
# set http daemon to run at boot time
systemctl enable httpd
mkdir /var/www/html/red
# populate /red directory of server with web page
cd /var/www/html/red
aws s3 cp s3://arr-bucket12398/hw-red.css ./
aws s3 cp s3://arr-bucket12398/hw-red-py.css ./
aws s3 cp s3://arr-bucket12398/python.png ./
aws s3 cp s3://arr-bucket12398/apache.svg ./
aws s3 cp s3://arr-bucket12398/red-index.html ./index.html
# adding metadata
EC2AZ=$(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/placement/availability-zone)
echo '<center><h1>This Amazon EC2 instance is located in Availability Zone: AZID </h1></center>' > /var/www/html/red/index.txt
sed "s/AZID/$EC2AZ/" /var/www/html/red/index.txt >> /var/www/html/red/index.html
# populate root of web server with web page (replaces apache default page)
cd /var/www/html
aws s3 cp s3://arr-bucket12398/hw-red.css ./
aws s3 cp s3://arr-bucket12398/hw-red-py.css ./
aws s3 cp s3://arr-bucket12398/python.png ./
aws s3 cp s3://arr-bucket12398/apache.svg ./
aws s3 cp s3://arr-bucket12398/red-root-index.html ./index.html
sed "s/AZID/$EC2AZ/" /var/www/html/red/index.txt >> /var/www/html/index.html
# optional restart of httpd daemon
systemctl restart httpd


