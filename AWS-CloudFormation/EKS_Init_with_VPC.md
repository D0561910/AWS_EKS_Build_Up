## 主要功能
1. 網路架構：

- VPC (10.0.0.0/16)
- 2個公共子網（用於 NAT Gateway）
- 2個私有子網（用於 EKS 節點）
- NAT Gateway 提供出站網路連接

2. EKS 叢集設定：

- 完全私有的 EKS 控制平面（EndpointConfigPublic: false）
- 受管理的節點群組
- 啟用所有叢集日誌記錄

3. 安全性：

- 適當的 IAM 角色和政策
- 安全群組配置
- VPC 端點（EC2、ECR、S3）確保私有連線

## 使用方式

- 部署模板：

```bash
aws cloudformation create-stack \
  --stack-name my-eks-private-cluster \
  --template-body file://eks-private-cluster.yaml \
  --capabilities CAPABILITY_IAM \
  --parameters \
    ParameterKey=ClusterName,ParameterValue=my-private-eks \
    ParameterKey=NodeGroupInstanceType,ParameterValue=t3.medium
```

2. 連接到私有叢集：由於這是完全私有的叢集，您需要：

- 從 VPC 內部的 EC2 實例連接
- 或設置 VPN/Direct Connect
- 或使用 AWS Systems Manager Session Manager


3. 配置 kubectl：

```bash
aws eks update-kubeconfig --region your-region --name my-private-eks
```

## 可自訂參數

- ClusterName: EKS 叢集名稱
- KubernetesVersion: Kubernetes 版本
- NodeGroupInstanceType: 工作節點實例類型
- NodeGroupDesiredSize: 期望的節點數量
- NodeGroupMinSize: 最小節點數量
- NodeGroupMaxSize: 最大節點數量
