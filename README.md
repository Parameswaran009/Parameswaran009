AWSTemplateFormatVersion: "2010-09-09"
Description: Elastic Beanstalk Application with Node.js Runtime, Auto Scaling Group, and ALB

Parameters:
  ExistingVpcId:
    Type: String
    Description: Existing VPC ID where resources will be deployed

  ApplicationName:
    Type: String
    Description: Elastic Beanstalk Application Name

  EnvironmentName:
    Type: String
    Description: Elastic Beanstalk Environment Name

  CustomTagKey:
    Type: String
    Description: Tag key for resources
    Default: "test"

  TagValue:
    Type: String
    Description: Tag value for resources
    Default: "test"

Resources:
  ElasticBeanstalk:
    Type: "AWS::ElasticBeanstalk::Application"
    Properties:
      ApplicationName: !Ref ApplicationName
      Description: "Elastic Beanstalk Application"

  ElasticBeanstalkEnvironment:
    Type: "AWS::ElasticBeanstalk::Environment"
    Properties:
      EnvironmentName: !Ref EnvironmentName
      ApplicationName: !Ref ElasticBeanstalk
      SolutionStackName: "64bit Amazon Linux 2 v5.8.6 running Node.js 18"
      OptionSettings:
      - Namespace: aws:autoscaling:launchconfiguration
        OptionName: IamInstanceProfile
        Value: aws-elasticbeanstalk-ec2-role   
      
      - Namespace: aws:elasticbeanstalk:environment
        OptionName: LoadBalancerType
        Value: application

      - Namespace: aws:autoscaling:trigger
        OptionName: MeasureName
        Value: CPUUtilization

      - Namespace: aws:autoscaling:trigger
        OptionName: Unit
        Value: Percent

      - Namespace: aws:autoscaling:trigger
        OptionName: UpperThreshold
        Value: 80

      - Namespace: aws:autoscaling:trigger
        OptionName: LowerThreshold
        Value: 30
      
      - Namespace: aws:autoscaling:asg
        OptionName: MinSize
        Value: 2

      - Namespace: aws:autoscaling:asg
        OptionName: MaxSize
        Value: 4

      Tier:
        Name: "WebServer"
        Type: "Standard"
      Tags:
        - Key: !Ref CustomTagKey
          Value: !Ref TagValue
