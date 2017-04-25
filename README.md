
# Blog Post- Programmatic Cloudformation Template

https://blog.awsmeter.io/programmatic-cloudformation-template/

run following cli to generate cloudformation yml in project root folder.

    cd template
	sh build.sh


## Fragment management
In some case, one AWS resource is used in multiple environments. We can use EJS include features. DEV,QA,PROD cloudformation template can include same resources.

	Resources:
	<% include ./partials/resources.yml %>
	  ...
   

## Variable interpolation
We can interpolate variable from options.json through <%= %> in EJS.

    apiTaskDefinition:
       Type: AWS::ECS::TaskDefinition
       DependsOn: ECSTaskRole
       Properties:
         ContainerDefinitions:
         - Name: api
           Essential: true
           Image: <%= container_path %>/api
           Memory: <%= container.api.memory %>
           Environment:
           - Name: REGION
             Value: !Ref "AWS::Region"
         TaskRoleArn: !Ref 'ECSTaskRole'
    


## Loop over list
There is a case we need to loop over all defined instances type(15+ instances) to create spot fleet instance launch configuration.

    Properties:
        SpotFleetRequestConfigData:
          IamFleetRole: !GetAtt iamFleetRole.Arn
          SpotPrice: !Ref 'ECSSpotPrice'
          TargetCapacity: !Ref 'DesiredCapacity'
          TerminateInstancesWithExpiration: false
          AllocationStrategy: lowestPrice
          LaunchSpecifications:
          <% for(var i in instancesTypes) {%>
          <% include ./partials/instance-launch-specification.yml %>
          <% } %>


