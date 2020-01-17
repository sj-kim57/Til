# aws 개요 정리

![목표 아키텍쳐 aws 리소스 VPC 구성 완료에 대한 이미지 검색결과](https://cloud-img.hosting.kr/wp-content/uploads/2018/02/09100954/app.png)

### aws계정

* root 계정: 

* IAM: 개별적 사용자 계정으로, 루트 계정은 각 부분에 맞는 IAM 계정을 생성하고 해당 담당자에게 계정을 전달하여 IAM계정은 그 부분만 작업할 수 있게 함

  * Group:
  * User
  * Role
  * Policy

  https://musma.github.io/2019/11/05/about-aws-iam-policy.html



### aws 서비스

* 관리형 서비스: EB(Paas), DynamoDB, RDS => aws에서 관리, 높은 비용

* D.I.Y(EC2기반) Iaas (CloudFomation) => 개인(고객)이 직접 관리, 낮은 비용

  

### 서비스 scope

* global: Route53, IAM, STS, ClousFront
* Region: S3, CloudTrail, EFS ...
* A.Z: EC2, EBS ...



Default(VPC) => public subnet에 ex2를 올리면 공인 ip 부여 가능

