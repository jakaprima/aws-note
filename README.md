# aws-note
operational reminder

biaya aws itu dilihat dari 
-penggunaan resource seperti CPU dan RAM, dan berapa lama makainya
-quantity of data stored in storage
-quantity data that transferent out from all services

setting billing alarm
- pojok kanan atas klik username -> my billing dashboard -> billing preference -> check receive billling alert, check receive free tier usage alert -> input email
- pilih cloudwatch service -> pilih menu alarm -> create alarm -> select metric -> select billing (harus pake N. virginia) -> total estimated charge -> select usd -> klik select metric -> di condition card pilih 5 dolar -> klik in alarm -> create new topic (Billing_Notification) -> buka email -> click confirm subscription -> klik next -> create alarm

SETTING IAM (Identity and access management) => file yang berisi permission yang di bolehkan untuk user tersebut
contoh (shared acess)
kelompok A bisa buka EC2 dan S3 tak bisa buka RDS
kelompok B buka S3
kelompok C cuma bisa buka S3 dan RDS doang

contoh granular permission
kelompok A cuma bisa buka staging
kelompok B cuma bisa buka production
kelompok C bisa buka ke duanya

contoh secure access (secure access)
buka gambar

Operational:
- pilih service IAM
- ganti url alias https://primasaja.signin.aws.amazon.com/console (optional)
- user groups -> create groups -> “Admins” -> click permission -> click AdministratorAccess -> view json file
- pilih menu users -> create new user -> input username -> check AWS console management -> uncheck reset password -> isi password pake custom password -> klik next -> next -> create user -> sekarang login pake password di primasaja.sigin -> check klik billing menu -> maka tidak ada permission untuk melihatnya

VPC = Virtual Private Cloud => lounch amazon resource to virtual network
Operational:
- create VPC -> name tag my-vpy-1 -> IPv4 10.0.0.0/16 -> tenancy default (kalo dedicated cepet webnya tapi makan biaya) -> create VPC
- check route table associated -> routenya akan sama 10.0.0.0/16
- check network ACL inbound rules dan outbound rules
- check subnet (belom ada karena harus buat EC2 yang tersambung dengan VPC baru ini)

ELASTIC IP
ada 2 type IP private an public (private )
untuk buat IP public buat Elastic IP address yang terassociated dengan NAT gateway
- buka vpc lagi klik menu elastic IP -> click allocate elastic ip address -> klik allocate


SUBNET
private subnet biasanya untuk database agar tidak sembarang orang bisa akses
public subnet untuk tersambung ke internet

Operational:
- klik subnet -> isi vpc pake vpc yang dibuat (my-vpc-1)
- subnet name 10.0.1.0_AP_SOUTHEST_1A_MY-VPC-1_PUB
- available zone = ap-southest-1a karena saya berada di situ yang paling deket
- IPv4 = 10.0.1.0/24
- buat lagi untuk yang private
- klik subnet -> isi vpc pake vpc yang dibuat (my-vpc-1)
- subnet name 10.0.2.0_AP_SOUTHEST_2B_MY-VPC-1_PRV
- available zone = ap-southest-1B karena saya berada di situ yang paling deket
- IPv4 = 10.0.2.0/24


INTERNET GATEWAY (agar VPC bisa konek ke internet)
requirement:
- internet gateway harus berisi VPC
- semua instance di subnet harus ada public IP atau elastic IP
- subnet route table harus point ke internet gateway
- semua network access control dan security group rules harus di setting unuk allow required traffic to and from instance

Operational:
- create internet gateway -> kasih nama my-internet-gateway-1
- status detach, masukin vpcnya -> action -> attach vpc -> my-vpc-1

Route Table = set of rules disebut routes, untuk menjelaskan kemana traffic internet diarahkan, 1 subnet hanya bisa terassociated dengan 1 route table, tapi semua subnet bisa memakai 1 route table

Operational: 
- create route table -> name my-RTB-1 -> 
- pilih vpc my-vpc-1
- edit routes tambahin ke internet destination: 0.0.0.0/0  target: my-internet-gateway-1
- klik subnet associations (belum terassosiated)
- klik edit -> pilih subnet yang tadi kita buat tapi yang public
subnet-0ef5e54fe022d3e91 | 10.0.1.0_AP_SOUTHEST_1A_MY-VPC-1_PUB


NAT DEVICE = (private subnet mungkin butuh internet access untuk berkomunikasi dengan aws resource lain)
Operational:
- copy subnet ID si subnet yang public subnet-0ef5e54fe022d3e91 karena new device akan ditaruh di subnet public
- create NAT gateway paste di input subnet
- pilih ELASTIC IP yang pernah kita buat diatas
- klik menu route tables -> pilih route table yang sudah dibuat sebelumnya di route table public
- edit route 0.0.0.0/0 target: nat gateway yang tadi dibuat  CREATE SECURITY GROUP
- security group name: my-webserver-SG
- vpc: pilih my-vpc-1
- inbound rules
- 1.1 HTTP source 0.0.0.0/0 karena untuk web server
- 1.2 HTTPS source 0.0.0.0/0 karena untuk web server
- 1.1 SSH source 0.0.0.0/0 (nanti diganti)
- security group name: my-dbserver-SG
- vpc: pilih my-vpc-1
- inbound rules
- 1.1 MSSQL sourcenya dari= my-webserver-SG
- 1.2 RDP source 0.0.0.0/0 (nanti diganti) untuk login 
