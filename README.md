# Desafio Implementando Infraestrutura Automatizada com AWS CloudFormation

Assim como o desafio da minha primeira stake, esse desafio foi desenvolvido com o objetivo de automatizar uma infraestrutura simples com:

1 - Uma instância EC2 como Servidor Web Apache, com uma página em HTML com a mensagem "Desafio 4: Stake no CloudFormation Servidor Web e Bucket S3!!!"

2- Um Grupo de Segurança (Security Group) com acesso a porta 80 (HTTP) e 22 (SSH).

3- Um buket S3 utilizado para armazenar arquivos estáticos com integração com a instância EC2 (Servidor Web).

Foi utiliado um tamplete em YAML e o mesmo foi implementado no CloudFormation.

O Servidor Web conta com um tipo de instância t3.micro, com o sistema operaconal Amazon Linux 2CPU 1GB, com 8 GB de memória RAM. Esse servidor foi configurado na região Norte da Virgina (us-east-1).

O bucket S3 foi criado com acesso privado com o nome isa-desafio4-cloudformation.

Na pasta image contém as imagens dessa automatização dessa infraestrutura. Também contém o arquivo YAML anexado 

Segue abaixo o template em YAML:

```
AWSTemplateFormatVersion: '2010-09-09'
Description: "Desafio DIO 4 - Infraestrutura Automatizada no AWS CloudFormation com EC2 + S3 + Security Group"

Resources:
  WebServerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: "Permitir acesso SSH e HTTP"
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0bdd88bd06d16ba03
      InstanceType: t3.micro
      SecurityGroupIds:
        - !Ref WebServerSecurityGroup
      KeyName: Name
      UserData:
        Fn::Base64: |
          #!/bin/bash
          yum update -y
          yum install -y httpd
          systemctl start httpd
          systemctl enable httpd
          echo "<h1>Desafio 4: Stake no CloudFormation Servidor Web e Bucket S3!!!</h1>" > /var/www/html/index.html
      Tags:
        - Key: Name
          Value: ServidorWeb

  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub "isa-desafio4-cloudformation-${AWS::AccountId}"
      AccessControl: Private
``
