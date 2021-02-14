#CloudFormation 

PointsToNote:

1. When creating non-default VPC , we cannot add securitygroup in instance resource , instead should use securitygroupId .
	- use function to extract security ID(attributes ) that being created in template .
2.we Cannot modify main/default route table entry from Cloudformation template .
	- use 'subnetroutetableassociation' to create a route table for that subnet that is reference with VPC created.
3. Always check aws cloudformation Documentations for resource and attributes   
