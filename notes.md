# AWS SAA-C02 筆記（繁體中文重點整理）

## 1.1. Cloud Computing Fundamentals
- 雲端運算五大特性：
  1. 隨需自助（On-Demand Self-Service）：可自行透過 UI/CLI 配置與終止資源，無需人工介入。
  2. 廣泛網路存取（Broad Network Access）：可透過標準協定於任何裝置存取服務。
  3. 資源彈性池（Resource Pooling）：資源集中管理，達到規模經濟。
  4. 快速彈性（Rapid Elasticity）：可自動依負載擴展或縮減資源。
  5. 量化服務（Measured Service）：按用量計費。
- 公有雲（Public Cloud）、私有雲（Private Cloud）、多雲（Multi-Cloud）、混合雲（Hybrid Cloud）定義。
- 雲端服務模型：
  - On-Premises：全部自行管理。
  - Data Center Hosting：僅管理設施。
  - Infrastructure as a Service（IaaS）：供應商管理設施與伺服器，客戶管理 OS 以上。
  - Platform as a Service（PaaS）：供應商管理執行環境，客戶僅管理應用程式與資料。
  - Software as a Service（SaaS）：直接使用軟體服務，幾乎無需管理。
  - 其他：Function as a Service、Container as a Service、Database as a Service。

## 1.2. AWS-Fundamentals
- AWS 支援方案：Basic、Developer、Business、Enterprise。
- Public/Private Services 僅指網路層級，不代表權限。
- AWS Global Infrastructure：
  - Region（區域）：全球多個地區部署完整 AWS 基礎設施。
  - Edge Location：本地分發點，降低延遲。
  - AZ（Availability Zone）：一個 Region 內有 2~6 個 AZ，彼此隔離且高速連線。
  - 服務彈性分級：Global（如 IAM、Route 53）、Region、AZ。
- Default VPC：每個 region 一個預設 VPC，CIDR 為 172.31.0.0/16，建議自建 VPC 以獲得彈性。
- EC2（Elastic Compute Cloud）：AWS 預設運算服務，提供虛擬機（Instance），按秒計費。
  - Instance 狀態：Running（全項目計費）、Stopped（僅 EBS 計費）、Terminated（不計費）。
  - AMI（Amazon Machine Image）：可用於建立或複製 Instance，僅限於同一 region。
  - 連線方式：Windows 用 RDP（3389），Linux 用 SSH（22），需使用金鑰對。
- S3（Simple Storage Service）：全球物件儲存服務，無限容量，物件最大 5TB，Bucket 名稱全球唯一。
- CloudFormation：基礎設施即程式碼（IaC）服務，支援 YAML/JSON 模板，管理 Stack。
- CloudWatch：監控服務，包含 Metrics、Logs、Events，可設警示（Alarm）。
- Shared Responsibility Model：AWS 負責雲端本身安全，客戶負責雲端內部安全。
- 高可用性（HA）、容錯（FT）、災難復原（DR）定義與差異。
- DNS 與 Route 53：域名註冊、Zone file、DNS record（A、AAAA、CNAME、MX、TXT）、TTL。

## 1.3. IAM-Accounts-AWS-Organizations
- IAM Policy：附加於 IAM User、Group、Role，控制資源存取權限。
  - Policy 組成：SID、Effect（Allow/Deny）、Action、Resource。
  - 優先順序：Explicit Deny > Explicit Allow > Default Deny。
  - Inline Policy（個別）、Managed Policy（共用，建議最佳實踐）。
- IAM User：長期存取 AWS 的身份（人、應用程式、服務帳號），可用帳密或 Access Key 登入。
  - Amazon Resource Name（ARN）：唯一標識 AWS 資源。
- IAM Group：僅用於管理 IAM User，無法直接登入或被資源授權。
- IAM Role：可被多個身份短期假扮，透過 Trust Policy 與 Permission Policy 控制。
  - 適用情境：Lambda 執行、緊急存取、企業 SSO、Web Identity Federation、跨帳號存取。
- AWS Organizations：集中管理多個 AWS 帳號，主帳號（Master Account）與成員帳號（Member Account），支援 Consolidated Billing。
  - Service Control Policies（SCP）：限制組織內帳號可用服務，僅作為限制，不授權。 

## 1.3.8. CloudWatch Logs
- CloudWatch Logs 可儲存、監控、存取日誌資料，支援 log stream、log group 與 metric filter。
- 設定權限與保留期於 log group 層級。

## 1.3.9. CloudTrail
- 追蹤 AWS API 呼叫與活動，預設保留 90 天 Event History。
- Trail 可記錄 Management Events（管理操作）與 Data Events（如 S3/Lambda 操作）。
- 支援單區域或全區域 trail，部分全球服務（如 IAM、STS、CloudFront）僅記錄於 us-east-1。
- 可將日誌輸出至 S3 或 CloudWatch Logs。

---

## 1.4. Simple Storage Service (S3)
### 1.4.1. S3 Security
- S3 預設為私有，僅 root user 有初始存取權。
- Bucket Policy（資源政策）可控跨帳號、匿名存取；Identity Policy 僅限本帳號身份。
- ACL（Access Control List）為舊式權限管理，AWS 不建議使用。

### 1.4.2. S3 Static Hosting
- 可將 S3 作為靜態網站主機，需 index 與 error 文件。
- 啟用後產生 website endpoint，可搭配自訂網域。
- 適合 offload 靜態內容、備援維護頁面。
- 費用依儲存、流量、操作次數計算。

### 1.4.3. Object Versioning & MFA Delete
- 預設未啟用 versioning，啟用後不可關閉（僅可暫停）。
- 支援多版本物件，刪除時會加上 delete marker。
- 啟用 MFA Delete 可強化刪除保護。

### 1.4.4. S3 Performance Optimization
- 單一 PUT 上限 5GB，超過需用 Multipart Upload（最小 100MB，最多 10,000 parts）。
- S3 Accelerated Transfer 利用 edge location 加速跨區傳輸。

### 1.4.5. Encryption 101
- Encryption at Rest：靜態加密，防止儲存設備遺失時資料外洩。
- Encryption in Transit：傳輸加密，防止中間人竊聽。
- 專有名詞：plaintext、key、ciphertext。
- 對稱式加密（Symmetric）、非對稱式加密（Asymmetric）、簽章（Signing）、隱寫術（Steganography）。

### 1.4.6. Key Management Service (KMS)
- KMS 為區域性服務，管理金鑰（CMK），支援對稱與非對稱金鑰。
- CMK 具備 Key Policy，支援金鑰輪替與別名（alias）。
- Data Encryption Key（DEK）用於加密大於 4KB 資料，KMS 僅產生不儲存 DEK。

### 1.4.8. S3 物件加密方式
- Client-Side Encryption：客戶端加密，AWS 無法解密。
- Server-Side Encryption：
  - SSE-C：客戶提供金鑰，S3 執行加解密。
  - SSE-S3（AES256）：S3 管理金鑰，低管理負擔但無法控管金鑰輪替。
  - SSE-KMS：KMS 管理 master key，支援更細緻權限與審計。 

## 1.4.9. S3 Storage Classes
- S3 Standard：預設儲存類型，三個 AZ 複寫，最高可用性與耐久性。
- S3 Standard-IA：低頻存取，適合備份、災難復原，30 天最短收費期。
- One Zone-IA：僅單一 AZ，適合可重建資料。
- S3 Glacier/Glacier Deep Archive：歸檔儲存，取回需數分鐘至數小時，適合長期備份。
- S3 Intelligent-Tiering：自動分層，依存取頻率自動調整儲存類型。

## 1.4.10. Object Lifecycle Management
- 可設定 Transition（轉換儲存類型）與 Expiration（自動刪除）規則，降低儲存成本。

## 1.4.11. S3 Replication
- 支援 Cross-Region Replication（CRR）與 Same-Region Replication（SRR）。
- 需雙方 bucket 均啟用 versioning，僅單向複製，無法 retroactive。
- 可選擇複製物件、儲存類型、擁有權，支援 RTC（Replication Time Control）。

## 1.4.12. S3 Presigned URL
- 產生帶時效的存取連結，授權他人安全存取 S3 物件。
- 權限以產生 URL 當下的身份為準。

## 1.4.13. S3 Select/Glacier Select
- 允許用 SQL-like 查詢僅取回部分物件內容，節省流量與時間。

---

## 1.5. Virtual Private Cloud (VPC)
### 1.5.1. 網路基礎
- IPv4、IPv6、CIDR、私有 IP（RFC1918）、Classful/Classless、Subnetting 概念。
- TCP/UDP、Port、Packet 結構。

### 1.5.2. VPC 規劃與結構
- VPC 最小 /28（16 IP）、最大 /16（65,536 IP），可加次要 CIDR。
- 建議避開常用 10.0/10.1，規劃多區域多帳號時預留空間。
- Subnet 建議依 AZ 與層級（web/app/db/spare）劃分。

### 1.5.3. Custom VPC
- 完全隔離、彈性配置、支援 Hybrid networking。
- Tenancy 可選 default（共用硬體）或 dedicated（專屬硬體）。
- IPv6 預設 /56，無私有位址，皆為 public。
- DNS 由 Route 53 提供，VPC base IP + 2。

### 1.5.4. VPC Subnet
- 每個 Subnet 僅屬於一個 AZ，CIDR 不可重疊。
- 每個 Subnet 有 5 個保留 IP（network、router、DNS、AWS 保留、broadcast）。
- DHCP Options Set 控制自動分配 IP，可更換但不可編輯。
- 可自動分配 public IPv4/IPv6。

### 1.5.5. VPC Routing & Internet Gateway
- VPC Router 內建於每個 VPC，負責子網間流量。
- Route Table 控制流量路由，subnet 只能綁定一個 route table。
- Internet Gateway（IGW）提供 VPC 與網際網路/公有 AWS 服務（如 S3）連接。 

### 1.5.5.3. Internet Gateway（IGW）
- IGW 為區域性資源，一個 VPC 僅能有一個 IGW，提供所有 AZ 公網連線。
- IGW 負責將 EC2 私有 IP 映射至公有 IP，僅在 IGW 層做轉換，EC2 內部僅見私有 IP。
- IPv6 公網連線不需轉換，直接由 IGW 轉發。

### 1.5.5.4. Bastion Host / Jumpbox
- 公網子網中的 EC2，作為管理進入點，連線後可存取私有資源。
- 可限制來源 IP、整合企業身份驗證。

### 1.5.6. Network Access Control List（NACL）
- NACL 為子網級防火牆，預設關聯所有子網，僅過濾進出子網的流量。
- 規則有編號，依序比對，遇到第一個符合即停止。
- 支援 Allow/Deny，最後一條為隱含 Deny。
- Stateless，進出流量需分別設規則。
- 適合需明確封鎖特定 IP/網段或無法用 SG 的資源。

### 1.5.7. Security Group（SG）
- 資源級防火牆，綁定於 EC2 等資源。
- Stateful，只需設入站規則，回應自動允許。
- 只支援 Allow，未明列即隱含 Deny。
- 可用資源 ID 作為來源/目的。
- SG 適合大多數情境，NACL 適合需明確封鎖。

### 1.5.8. NAT Gateway
- NAT Gateway 提供私有子網主機對外連線（如更新套件），隱藏內部 IP。
- 僅支援出站連線，無法做為 Bastion Host 或 Port Forwarding。
- 需部署於公網子網並綁定 Elastic IP。
- 高可用於單一 AZ，跨 AZ 需多個 NATGW。
- IPv6 不需 NAT，皆為公網可路由。
- 收費依流量與小時計算。

---

## 1.6. Elastic Cloud Compute（EC2）
### 1.6.1. 虛擬化技術
- EC2 為 IaaS 服務，提供虛擬機（VM）。
- 傳統架構：CPU、Kernel（特權模式）、User Mode（應用程式）。
- Emulated Virtualization：軟體模擬硬體，需 binary translation，效能較低。
- Para-Virtualization：修改 Guest OS，改用 hypercall 呼叫 HV，提升效能。
- Hardware Assisted Virtualization：硬體支援虛擬化，CPU 直接攔截特權指令，效能最佳。 

### 1.6.2. EC2 架構與彈性
- Tenancy 分為 Shared、Dedicated、Dedicated Host。
- EC2 實例僅運行於單一 AZ，無法跨 AZ 移動（僅能複製）。
- ENI（Elastic Network Interface）可配置多張網卡於同一 AZ。
- 本地儲存（Instance Store）為暫存，重啟/遷移即遺失。
- 永久儲存建議用 EBS（Elastic Block Store）。

### 1.6.3. EC2 Instance Types
- General Purpose（T, M）：均衡型，預設選擇。
- Compute Optimized（C）：運算密集型。
- Memory Optimized（R, X）：記憶體密集型。
- Accelerated Computing（P, G, F）：GPU/FPGA。
- Storage Optimized（H, I, D）：高 IOPS/大容量本地儲存。
- 命名規則：如 R5dn.8xlarge，家族/世代/大小/特殊功能。

### 1.6.4. 儲存類型
- Instance Store：本地直連，極高速，暫存性。
- EBS（Elastic Block Store）：網路掛載，持久性，僅限同 AZ。
- Block Storage（如 EBS）：可掛載/開機。
- File Storage：如 EFS，檔案結構存取。
- Object Storage：如 S3，平面物件存取。

### 1.6.5. EBS 詳細
- 僅限同 AZ，資料於該 AZ 內多副本。
- 四種型態：
  - gp2（General Purpose SSD）：預設，適合大多數情境。
  - io1（Provisioned IOPS SSD）：高 IOPS，支援 Multi-Attach。
  - st1（Throughput Optimized HDD）：高吞吐，適合日誌/媒體。
  - sc1（Cold HDD）：低頻存取。
- 收費依容量與型態。
- EBS 最高 80k IOPS/實例，64k IOPS/卷（io1）。

### 1.6.6. Instance Store
- 僅能於啟動時掛載，與實體主機綁定。
- 主機異動/重啟/故障即遺失資料。
- AWS 最高效能儲存，適合暫存、快取。
- 價格已含於實例費用。 

### 1.6.7. EBS vs Instance Store
- EBS 適合需高可用、持久、可獨立於 EC2 的儲存需求。
- Instance Store 適合高 IOPS、暫存、stateless 服務。

### 1.6.8. EBS Snapshot
- 快照備份至 S3，具區域韌性，支援增量備份。
- 可用於跨 AZ/主機遷移、資料還原。
- 支援 Fast Snapshot Restore（FSR）即時還原。
- 收費依實際儲存資料量（GB/月）。
- EBS 加密用 KMS 金鑰，支援預設加密與自管金鑰。

### 1.6.9. ENI（Elastic Network Interface）
- 每台 EC2 至少一張 ENI，可有多張、跨子網（同 AZ）。
- ENI 綁定 Security Group、私有/公有/彈性 IP、MAC。
- 公有 IP 為動態，重啟/停啟動會變動。
- 彈性 IP（Elastic IP）可綁定於 ENI，靜態公網位址。

### 1.6.10. AMI（Amazon Machine Image）
- EC2 映像檔，含權限、EBS 快照、裝置對應。
- 僅限單一區域，可複製至他區。
- AMI 不可編輯，需新建。
- 收費依快照儲存容量。

### 1.6.11. EC2 價格模式
- On-Demand：隨用隨付，彈性最高。
- Spot：剩餘資源，最高可省 90%，價格浮動，隨時可能終止。
- Reserved：預付 1/3 年，最高省 75%，適合穩定長期需求。

### 1.6.12. Instance Status Check & Auto Recovery
- 系統/實例層級健康檢查，支援自動修復、重啟、終止。

### 1.6.13. Scaling
- Vertical Scaling：升級單一實例規格，需重啟。
- Horizontal Scaling：多實例分流，需 Load Balancer，適合 stateless 架構。

### 1.6.14. Instance Metadata
- 內部網址 http://169.254.169.254/latest/meta-data/ 提供實例資訊，無認證。

---

## 1.7. Containers & ECS
### 1.7.1. 容器基礎
- 容器（如 Docker）以多層映像檔（Image）組成，運行時加上可寫層。
- 映像檔可上傳至 Container Registry（如 Docker Hub、ECR）。
- 容器輕量、可攜、共用底層 OS，需 expose port。

### 1.7.2. ECS（Elastic Container Service）
- 受管容器編排服務，支援 EC2 與 Fargate 模式。
- ECS Cluster 管理容器運行、調度、placement。
- Container Definition 定義映像檔、port、IAM Task Role。
- Task Definition 定義資源、角色、環境。
- ECS Service 控制 Task 數量與高可用。
- EC2 模式需自管主機，Fargate 為 serverless，僅付資源費。
- ECR（Elastic Container Registry）為 AWS 受管映像檔倉庫。 

---

## 1.8. Advanced EC2
### 1.8.1. Bootstrapping with User Data
- User Data 於實例啟動時執行一次，透過 http://169.254.169.254/latest/user-data 存取。
- 限制 16KB，不加密，不應傳送密碼。
- 適合啟動時配置，可與 AMI Baking 搭配減少啟動時間。

### 1.8.2. CloudFormation Init（cfn-init）
- 比 User Data 更強大，支援 desired state 配置。
- 可管理套件版本、OS 使用者/群組、下載檔案。
- 透過 CreationPolicy 與 Signals 確保配置完成。

### 1.8.3. EC2 Instance Role
- 最佳實踐，EC2 可假扮 IAM Role 存取 AWS 資源。
- Instance Profile 將權限傳入實例。
- 憑證透過 metadata 存取，自動輪換。

### 1.8.4. Parameter Store
- 儲存配置與密鑰（String、StringList、SecureString）。
- 支援階層、版本、KMS 加密。
- 可儲存公開參數（如最新 AMI ID）。
- 需 IAM 權限存取。

### 1.8.5. CloudWatch Agent
- 需安裝 CloudWatch Agent 才能收集實例內日誌。
- 需 IAM Role 權限與配置檔案。
- 可透過 Parameter Store 管理配置。

### 1.8.6. EC2 Placement Groups
- Cluster：實例緊密放置，最高效能，10Gbps 單流，同 AZ。
- Spread：實例分散放置，最高可用性，每 AZ 最多 7 台。
- Partition：分組分散，每 AZ 最多 7 個 partition，適合 HDFS。

### 1.8.7. Dedicated Hosts
- 專屬硬體，無實例費用，適合軟體授權需求。
- 支援 RAM 跨帳號共享。
- 限制：部分 AMI、RDS、Placement Groups 不支援。

### 1.8.8. Enhanced Networking
- 使用 SR-IOV，提供更高頻寬、更低延遲。
- EBS Optimized 提供專用 EBS 網路容量。

---

## 1.9. Route 53
### 1.9.1. Public/Private Hosted Zones
- Public Hosted Zone：公開 DNS 資料庫，全球韌性。
- Private Hosted Zone：僅 VPC 內存取，支援 Split-view。
- 每月收費 + 查詢費用。

### 1.9.2. Health Checks
- 每 30 秒檢查（可調至 10 秒），支援 TCP、HTTP/HTTPS、字串匹配。
- 三種檢查：Endpoint、CloudWatch Alarm、Calculated。

### 1.9.3. Routing Policies
- Simple：單一資源，無健康檢查。
- Failover：主備切換，需健康檢查。
- Weighted：權重分配，支援遷移。
- Latency-based：依延遲選擇最近資源。
- Geolocation：依地理位置選擇，支援授權限制。
- Multi-value：多記錄健康檢查，移除不健康記錄。

---

## 1.10. Relational Database Service（RDS）
### 1.10.1. 資料庫類型
- Relational（SQL）：結構化，預先定義 schema，支援 JOIN。
- Non-Relational（NoSQL）：
  - Key-Value：快速、可擴展，適合快取。
  - Wide Column Store：如 DynamoDB，支援 partition key + sort key。
  - Document：JSON/XML 格式，適合巢狀資料。
  - Row Database（MySQL）：OLTP，適合交易處理。
  - Column Database（Redshift）：OLAP，適合分析查詢。 

### 1.10.2. EC2 上的資料庫
- 不建議在 EC2 上運行資料庫，管理複雜、缺乏高可用、效能較差。
- 僅在需要 OS 存取、特殊版本、AWS 不支援時考慮。

### 1.10.3. RDS 基礎
- 受管資料庫服務，支援 MySQL、MariaDB、PostgreSQL、Oracle、SQL Server。
- 使用標準資料庫引擎，可用相同工具存取。
- 支援不同實例類型（db.m5、db.r5、db.t3）。
- 儲存支援 io1、gp2、magnetic。

### 1.10.4. RDS Multi-AZ
- 在另一 AZ 建立 standby replica，同步複寫。
- 僅透過 CNAME 存取，standby 不可直接存取。
- 故障切換 60-120 秒，提供高可用性非容錯。
- 備份從 standby 執行，不影響效能。

### 1.10.5. RDS 備份與還原
- RPO（Recovery Point Objective）：最大資料遺失時間。
- RTO（Recovery Time Objective）：災難復原時間。
- 自動備份：每日快照 + 每 5 分鐘交易日誌。
- 手動快照不自動過期，還原建立新實例。

### 1.10.6. RDS Read Replicas
- 非同步複寫，可跨區域，每實例最多 5 個。
- 提升讀取效能，可鏈式複寫（延遲會累積）。
- 故障時可提升為主要實例，但無法逆轉。

### 1.10.7. Enhanced Monitoring
- 從實例內 agent 收集指標，比 CloudWatch 更詳細。

### 1.10.8. Amazon Aurora
- 叢集架構：主要實例 + 0-15 個副本。
- 共享儲存（最大 64 TiB），6 副本跨 AZ。
- 副本可讀取，故障切換快速。
- 支援 Cluster endpoint（讀寫）、Reader endpoint（讀取負載平衡）。

### 1.10.9. Aurora Serverless
- 使用 ACU（Aurora Capacity Units），可縮至 0 暫停。
- 按秒計費，適合不常用應用、開發測試。
- 透過 proxy fleet 管理連線。

### 1.10.10. Aurora Global Database
- 跨區域災難復原，最多 5 個次要區域。
- 儲存層複寫，約 1 秒延遲。
- 次要區域最多 16 個副本。

### 1.10.11. Aurora Multi-Master
- 多個讀寫實例，無需 endpoint 負載平衡。
- 寫入需多數節點同意，故障時自動切換。

### 1.10.12. Database Migration Service（DMS）
- 受管資料庫遷移服務。
- 支援 Full Load、CDC、Schema 轉換。
- 需 replication instance 執行遷移任務。

---

## 1.11. Network Storage - EFS
### 1.11.1. EFS 架構
- 實作 NFSv4，Linux 專用。
- 檔案儲存，可多實例共享。
- 私有服務，透過 mount targets 存取。
- 支援 VPC peering、VPN、Direct Connect 外部存取。

### 1.11.2. EFS 特性
- 兩種效能模式：General Purpose（預設）、Max I/O。
- 兩種吞吐模式：Bursting、Provisioned。
- 兩種儲存類型：Standard、Infrequent Access。
- 支援生命週期政策。

---

## 1.12. HA and Scaling
### 1.12.1. Load Balancer 基礎
- 解決單點故障、負載不均、DNS 快取問題。
- 提供高可用性與可擴展性。 

### 1.12.2. Application Load Balancer（ALB）
- Layer 7 負載平衡器，可檢查 HTTP/HTTPS 內容。
- 支援 path-based、host-based 路由規則。
- 支援 EC2、EKS、Lambda、HTTP/2、WebSocket。
- 支援 SNI（Server Name Indication）多 SSL 憑證。
- 收費：小時費率 + LCU（Load Balancer Capacity Unit）。
- Cross-zone load balancing 確保負載均衡。

### 1.12.3. Launch Configuration & Launch Template
- 預先定義 EC2 實例配置（AMI、類型、儲存、網路等）。
- Launch Template 較新，支援更多功能（T2/T3 unlimited、placement groups 等）。
- 不可編輯，需新建版本。

### 1.12.4. Auto Scaling Groups（ASG）
- 自動擴展與自我修復 EC2 實例。
- 使用 Launch Configuration 或 Launch Template。
- 控制三個值：minimum、desired、maximum。
- 支援三種擴展政策：
  - Manual：手動調整。
  - Scheduled：時間排程。
  - Dynamic：Simple、Stepped、Target。
- Cooldown Period 避免頻繁擴展。
- 可使用 ALB 健康檢查。

### 1.12.5. Network Load Balancer（NLB）
- Layer 4 負載平衡器，僅理解 TCP/UDP。
- 延遲最低，可處理數百萬請求/秒。
- 唯一可提供靜態 IP 的負載平衡器。
- 支援 SSL pass-through。

### 1.12.6. SSL Offload & Session Stickiness
- Bridging：ALB 解密後重新加密，可檢查 HTTP 內容。
- Pass-through：NLB 不處理 SSL，直接轉發。
- Offload：終止於負載平衡器，後端用 HTTP。
- Session Stickiness：使用 AWSALB cookie 確保同一用戶連至同一後端。

---

## 1.13. Serverless & App Services
### 1.13.1. 架構演進
- Monolithic：單一黑盒，故障一起、擴展一起、計費一起。
- Tiered：分層架構，使用負載平衡器，仍緊耦合。
- Queue-based：使用佇列解耦，支援 FIFO。
- Microservices：小型獨立服務，各自邏輯、資料、I/O。
- Event-driven：事件驅動，生產者/消費者模式，僅在有事件時消耗資源。

### 1.13.2. AWS Lambda
- Function-as-a-Service（FaaS），事件驅動執行。
- 支援多種 runtime（Python、Node.js 等）。
- 執行時間限制 15 分鐘。
- 每次執行獲得新的 runtime environment。
- 使用 execution role 存取 AWS 資源。
- 免費額度：1M 請求/月 + 400,000 GB-seconds。

### 1.13.3. CloudWatch Events & EventBridge
- 處理 AWS 服務事件與第三方事件。
- 支援規則模式匹配與排程。
- EventBridge 為 CloudWatch Events V2，功能更豐富。

### 1.13.4. API Gateway
- 受管 API 服務，提供認證、監控、安全。
- 收費：API 呼叫次數、資料傳輸量、快取。
- 適合作為 serverless 架構入口點。
- 支援架構演進，端點不變。

### 1.13.5. Serverless 架構
- 無需管理伺服器，使用 FaaS 與受管服務。
- 事件驅動，僅在有事件時消耗資源。
- 使用 S3、DynamoDB、Cognito 等受管服務。
- 範例：影片上傳 → S3 → Lambda 轉碼 → DynamoDB。

### 1.13.6. Simple Notification Service（SNS）
- 高可用、持久、PUB/SUB 訊息服務。
- 訊息最大 256KB，不適合大型二進位檔案。
- Publisher 發送訊息至 Topic，Subscriber 接收。
- 支援多種訂閱者：HTTP/HTTPS、Email、SQS、Lambda、SMS。
- 支援 Fanout 模式，單一 Topic 多個 SQS 佇列。 

### 1.13.7. AWS Step Functions
- 建立狀態機（State Machine）工作流程。
- 支援 Standard（1 年限制）與 Express（5 分鐘限制）。
- 狀態類型：Succeed/Fail、Wait、Choice、Parallel、Map、Task。
- 使用 Amazon States Language（ASL）JSON 格式定義。

### 1.13.8. Simple Queue Service（SQS）
- 受管高可用訊息佇列服務。
- 訊息最大 256KB，支援 Visibility Timeout、Dead-letter Queue。
- 兩種佇列類型：
  - Standard Queue：多車道，至少一次傳遞。
  - FIFO Queue：單車道，恰好一次傳遞，3000 msg/s（批次）。
- 收費依請求次數，非訊息數量。
- 支援短輪詢（立即）與長輪詢（最多 20 秒等待）。

### 1.13.9. Kinesis
- 可擴展串流服務，設計用於大量裝置/應用程式資料注入。
- Stream 儲存 24 小時移動視窗（可延至 7 天）。
- 多個消費者可存取同一資料視窗。
- 每個 shard 支援 1MB/s 注入、2MB/s 消費。
- Kinesis Data Firehose 可將資料持久化至 S3。

### 1.13.10. SQS vs Kinesis
- Kinesis：大吞吐量、多消費者、滾動視窗、資料分析。
- SQS：單一生產者、單一消費群組、非同步通訊、處理後刪除。

---

## 1.14. CDN and Optimization
### 1.14.1. CloudFront 基礎
- 全球物件快取（CDN），僅下載快取。
- Origin：原始內容位置（S3、ALB 等）。
- Distribution：CloudFront 配置單位。
- Edge Locations：全球快取基礎設施（200+ 位置）。
- Regional Edge Cache：支援本地 edge locations 的大型快取。

### 1.14.2. AWS Certificate Manager（ACM）
- 建立、更新、部署 SSL/TLS 憑證。
- 僅支援受管服務（CloudFront、ALB、API Gateway 等）。
- 不支援 EC2，CloudFront 需受信任簽署憑證。

### 1.14.3. Origin Access Identity（OAI）
- CloudFront 分配的身份，edge locations 獲得此身份。
- 調整 S3 bucket policy 允許 OAI 存取。
- 防止直接存取 S3，僅透過 CloudFront。

### 1.14.4. Lambda@Edge
- 在 Edge Locations 執行輕量 Lambda 函數。
- 僅支援 Node.js 與 Python，不支援 VPC。
- 使用案例：A/B 測試、裝置偵測、國家內容。

### 1.14.5. AWS Global Accelerator
- 將 AWS 網路移近客戶，優化資料流。
- 使用 Anycast IP 地址，流量透過 AWS 全球骨幹網路。
- 適用於 TCP/UDP 協議，非 HTTP/HTTPS。

---

## 1.15. Advanced VPC
### 1.15.1. VPC Flow Logs
- 擷取封包元資料（非內容），支援 VPC、子網、介面層級。
- 非即時，可輸出至 S3 或 CloudWatch Logs。
- 排除：instance metadata、AWS 時間同步、DNS、授權伺服器。

### 1.15.2. Egress-Only Internet Gateway
- IPv6 專用，僅允許出站連線。
- 類似 NAT，但適用於 IPv6（所有 IPv6 皆為公網可路由）。

### 1.15.3. VPC Gateway Endpoints
- 提供 S3 與 DynamoDB 私有存取。
- 使用 prefix list 與路由表，無需應用程式修改。
- 僅限同區域，高可用設計。

### 1.15.4. VPC Interface Endpoints
- 提供其他 AWS 公有服務私有存取。
- 使用 PrivateLink，需每個 AZ 一個 endpoint 實現高可用。
- 支援 Regional DNS 與 Zonal DNS。
- 可啟用 PrivateDNS 覆蓋預設服務 DNS。

### 1.15.5. VPC Peering
- 兩個 VPC 間私有加密網路連線。
- 支援跨區域、跨帳號，不支援傳遞式 peering。
- CIDR 不可重疊，同區域可參考 Security Group ID。

---

## 1.16. Hybrid and Migration
### 1.16.1. AWS Site-to-Site VPN
- VPC 與本地網路間 IPSec 加密連線。
- 支援靜態與動態路由（BGP）。
- 速度上限 1.25 Gbps，延遲不穩定（使用公網）。
- 適合作為 Direct Connect 備援。

### 1.16.2. AWS Direct Connect（DX）
- 專用光纖連線，1/10 Gbps 速度。
- 提供一致低延遲，無加密。
- 支援 Private VIF（VPC）與 Public VIF（公有服務）。
- 可透過 Public VIF 建立 IPSec VPN 實現加密。 

### 1.16.3. AWS Transit Gateway（TGW）
- 網路傳輸中心，連接 VPC 與本地網路。
- 支援傳遞式路由，減少網路複雜度。
- 單一網路閘道物件，高可用且可擴展。
- 支援 VPC、VPN、Direct Connect 附件。
- 可跨區域對等，支援 RAM 跨帳號共享。

### 1.16.4. Storage Gateway
- 混合儲存虛擬應用程式，可本地或 AWS 運行。
- 三種模式：
  - Tape Gateway（VTL）：虛擬磁帶儲存於 S3。
  - File Gateway（SMB/NFS）：檔案儲存備份至 S3。
  - Volume Gateway：區塊儲存備份至 S3/EBS。
    - Stored Mode：資料本地保存。
    - Cached Mode：資料主要儲存於 AWS。

### 1.16.5. Snowball / Edge / Snowmobile
- Snowball：50/80TB，1/10 Gbps，無運算。
- Snowball Edge：含運算，三種類型：
  - Storage Optimized：80TB，24 vCPU。
  - Compute Optimized：100TB，52 vCPU。
  - Compute with GPU：含 GPU。
- Snowmobile：貨櫃式資料中心，100PB 容量。

### 1.16.6. AWS Directory Service
- 受管 Active Directory 實作。
- 三種模式：
  - Simple AD：簡單需求。
  - Microsoft AD：Windows 或需信任關係。
  - AD Connector：代理至本地目錄。

### 1.16.7. AWS DataSync
- 資料傳輸服務，支援大規模遷移。
- 每個 agent 處理 10 Gbps，100TB/天。
- 支援增量、排程、壓縮、加密傳輸。
- 整合 S3、EFS、FSx。

### 1.16.8. FSx for Windows File Server
- 受管 Windows 檔案伺服器。
- 支援 Single/Multi-AZ，整合 Directory Service。
- 支援 VSS、DFS、Windows 權限模型。

### 1.16.9. FSx for Lustre
- 專為 HPC、機器學習、大數據設計。
- 100 GB/s 吞吐量，亞毫秒延遲。
- 支援 Persistent（HA）與 Scratch（純效能）部署。

---

## 1.17. Security, Deployment & Operations
### 1.17.1. AWS Secrets Manager
- 專為密鑰、密碼、API 金鑰設計。
- 支援自動輪換，整合 RDS。
- 使用 KMS 加密，整合 IAM 權限控制。

### 1.17.2. AWS Shield & WAF
- Shield Standard：免費，Layer 3/4 DDoS 防護。
- Shield Advanced：$3000/月，包含進階回應團隊。
- WAF：Layer 7 防火牆，防護 SQL 注入、XSS、地理封鎖。
- WEBACL 整合 Load Balancer、API Gateway、CloudFront。

### 1.17.3. CloudHSM
- 單租戶硬體安全模組，FIPS 140-2 Level 3。
- AWS 無法存取，支援 PKCS#11、JCE、CNG。
- 可作為 KMS 自訂金鑰儲存。
- 適用於 SSL/TLS 卸載、Oracle TDE、憑證授權。

---

## 1.18. NoSQL & DynamoDB
### 1.18.1. DynamoDB 架構
- NoSQL 資料庫即服務，寬欄鍵值資料庫。
- 支援 Provisioned 與 On-Demand 容量模式。
- 高韌性跨 AZ，可選全球韌性。
- 單數毫秒存取，支援備份與加密。

### 1.18.2. DynamoDB 表格
- 表格：共享相同主鍵的項目群組。
- 主鍵類型：Simple（Partition）、Composite（Partition + Sort）。
- 項目最大 400KB，屬性可選。
- 容量 = 速度：WCU（寫入）、RCU（讀取）。

### 1.18.3. DynamoDB 備份
- On-demand Backups：手動完整備份。
- Point-in-time Recovery：35 天連續記錄，1 秒粒度。

### 1.18.4. DynamoDB 操作
- On-Demand：未知負載，無管理開銷。
- Provisioned：設定 RCU/WCU。
- 1 RCU = 4KB 讀取/秒，1 WCU = 1KB 寫入/秒。
- 每個表格有 500 秒爆發池。

### 1.18.5. DynamoDB 查詢
- Query：需指定 Partition Key，可選 Sort Key。
- Scan：最不效率但最靈活，逐項目掃描。
- 一致性模型：
  - Eventually Consistent：較便宜，可擴展。
  - Strongly Consistent：較昂貴，使用 leader node。

### 1.18.6. DynamoDB Streams
- 24 小時滾動視窗的變更記錄。
- 支援四種檢視類型：KEYS_ONLY、NEW_IMAGE、OLD_IMAGE、NEW_AND_OLD_IMAGES。
- 可觸發 Lambda 函數，適合報告、分析、通知。

---

## 總結
本筆記涵蓋 AWS SAA-C02 考試重點，包含：
- 雲端基礎、AWS 架構、IAM、S3、VPC、EC2
- 容器、資料庫、負載平衡、自動擴展
- Serverless、CDN、混合架構、安全服務
- 監控、備份、災難復原、成本優化
