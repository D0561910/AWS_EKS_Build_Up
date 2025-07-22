## 主要變更
- 移除的元件：
- VPC、子網、路由表、NAT Gateway、Internet Gateway 創建
- 改為使用參數輸入現有資源 ID

### 新增的參數：

- VpcId: 現有 Shared VPC 的 ID
- PrivateSubnetId1/2: 私有子網 ID（用於 EKS 節點）
- PublicSubnetId1/2: 公共子網 ID（可選，用於 VPC 端點）
- CreateVPCEndpoints: 是否創建 VPC 端點
- NodeGroupDiskSize: 節點群組磁碟大小

### 部署範例
```bash
aws cloudformation create-stack \
  --stack-name my-eks-private-cluster \
  --template-body file://eks-private-cluster.yaml \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameters \
    ParameterKey=ClusterName,ParameterValue=my-private-eks \
    ParameterKey=VpcId,ParameterValue=vpc-xxxxxxxx \
    ParameterKey=PrivateSubnetId1,ParameterValue=subnet-xxxxxxxx \
    ParameterKey=PrivateSubnetId2,ParameterValue=subnet-yyyyyyyy \
    ParameterKey=PublicSubnetId1,ParameterValue=subnet-zzzzzzzz \
    ParameterKey=PublicSubnetId2,ParameterValue=subnet-wwwwwwww \
    ParameterKey=NodeGroupInstanceType,ParameterValue=t3.medium \
    ParameterKey=NodeGroupDesiredSize,ParameterValue=2 \
    ParameterKey=CreateVPCEndpoints,ParameterValue=Yes
```

### 重要特性

1. 安全群組：
- 專門為 EKS 優化的安全群組規則
- 控制平面和工作節點間的安全通信

2. VPC 端點：
- 可選擇性創建 ECR、EC2、S3 的 VPC 端點
- 支援完全私有的容器映像拉取

3. IAM 角色：
- 包含 SSM 管理權限，方便節點管理
- 遵循最佳實務的最小權限原則

4. 輸出值：
- 提供 kubectl 配置命令
- OIDC 發行者 URL（用於 IRSA）
- 安全群組 ID
