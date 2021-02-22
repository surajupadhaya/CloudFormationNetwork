#CloudFormation 

PointsToNote:

1. When creating non-default VPC , we cannot add securitygroup in instance resource , instead should use securitygroupId .
	- use function to extract security ID(attributes ) that being created in template .
  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      AvailabilityZone: us-east-1a
      ImageId: ami-a4c7edb2 #YourPreferedImage
      InstanceType: t2.micro #YourPreferedInstancesType
      SecurityGroupIds:
        - !GetAtt SSHSecurityGroup.GroupId
      SubnetId: !Ref Subnet1
      KeyName: 'mupair'
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: "10.0.0.0/16"
  VPCInternetAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MyVPC
      InternetGatewayId: !Ref InternetGateway
  InternetGateway:
    Type: AWS::EC2::InternetGateway

2. we Cannot modify main/default route table entry from Cloudformation template .

	- use 'subnetroutetableassociation' to create a route table for that subnet that is reference with VPC created.

myRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId:
        Ref: MyVPC
  Myroute:
    Type: AWS::EC2::Route

    Properties:
      DestinationCidrBlock: "0.0.0.0/0"
      GatewayId: !Ref InternetGateway
      RouteTableId: !Ref myRouteTable
  SubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref Subnet1
      RouteTableId: !Ref  myRouteTable

   

3. ##########AutoScaling Config with CPU traking Policy#############
 
  Scaling:
    Type: AWS::AutoScaling::AutoScalingGroup
    Condition: CreateProdResources
    Properties:
      AvailabilityZones:
        - 'us-east-1a'
      InstanceId: !Ref MyInstance
      MaxSize: '5'
      MinSize: '1'
      DesiredCapacity: '2'
      MetricsCollection:
        - Granularity: 1Minute

  ScalingPolicy:
    Type: AWS::AutoScaling::ScalingPolicy
    Properties:
      AutoScalingGroupName: !Ref Scaling
      PolicyType: 'TargetTrackingScaling'
      TargetTrackingConfiguration:
        PredefinedMetricSpecification:
          PredefinedMetricType: ASGAverageCPUUtilization
        TargetValue:  50
4. NONAlphanumeric Reource Name are not Supported.

5. Always check aws cloudformation Documentations for resource and attributes

