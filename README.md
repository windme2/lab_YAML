# YAML Practice - Complete CI/CD Project

## 🏗️ Project Structure
```
lab_YAML/
├── .github/
│   └── workflows/
│       └── ci.yml              # GitHub Actions CI/CD Pipeline
├── k8s/
│   ├── deployment.yml          # Kubernetes Deployment (2 replicas)
│   └── service.yml             # Kubernetes NodePort Service
├── docker-compose.yml          # Docker services (Nginx + MySQL)
└── README.md                   # Documentation with answers
```

## 📋 Project Overview
โปรเจค CI/CD แบบครบวงจร ที่ใช้ YAML หลายไฟล์ประกอบด้วย GitHub Actions, Docker Compose และ Kubernetes

## 🚀 การใช้งาน

### รัน Docker Compose:
```bash
docker-compose up -d
```
- เข้าถึงเว็บไซต์: http://localhost:8080 (Nginx default page)
- เข้าถึง MySQL: localhost:3306

### Deploy ไปยัง Kubernetes:
```bash
kubectl apply -f k8s/
```
- เข้าถึงเว็บไซต์: http://localhost:30080

### ตรวจสอบ CI/CD:
- Push โค้ดไปยัง GitHub
- ดู Actions tab เพื่อติดตาม workflow

## 📊 Architecture Diagram

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Developer     │    │   GitHub Actions │    │   Deployment    │
│   Push Code     │───▶│   CI/CD Pipeline │───▶│   Environment   │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                                ▼                        ▼
                       ┌──────────────────┐    ┌─────────────────┐
                       │   Validation:    │    │   Target Env:   │
                       │   • YAML Lint    │    │                 │
                       │   • Docker Test  │    │ ┌─────────────┐ │
                       │   • K8s Dry-run  │    │ │Docker Compose│ │
                       └──────────────────┘    │ │(Development)│ │
                                               │ └─────────────┘ │
                                               │ ┌─────────────┐ │
                                               │ │ Kubernetes  │ │
                                               │ │(Production) │ │
                                               │ └─────────────┘ │
                                               └─────────────────┘
```

## 🔄 CI/CD Workflow Process

1. **Code Push** → GitHub repository
2. **GitHub Actions Triggered** (ci.yml)
3. **Pipeline Execution:**
   - Lint YAML files
   - Build application
   - Validate Docker Compose
   - Test Kubernetes manifests (dry-run)
   - Notify completion
4. **Ready for Deployment** to target environments

---

## 📝 Answer Questions

### โจทย์ที่ 1: GitHub Actions Workflow

**คำถาม 1.1:** อธิบายว่าทำไมต้องใช้ `on: push` และ `on: pull_request`

**คำตอบ:** 
- **`on: push`**: ใช้เพื่อให้ workflow ทำงานทันทีเมื่อมีการ push โค้ดไปยัง branch main เพื่อตรวจสอบโค้ดใหม่และหาปัญหาได้รวดเร็ว ป้องกันโค้ดที่มีปัญหาเข้าสู่ production
- **`on: pull_request`**: ใช้เพื่อตรวจสอบความถูกต้องของโค้ดก่อนที่จะ merge เข้า main branch ทำให้สามารถ review และแก้ไขปัญหาได้ก่อนที่โค้ดจะเข้าสู่ระบบจริง

**คำถาม 1.2:** อธิบายว่า `jobs` และ `steps` ทำหน้าที่อะไร

**คำตอบ:** 
- **jobs**: เป็นกลุ่มของงานที่ทำงานแยกกันและสามารถทำงานพร้อมกันหรือต่อเนื่องกัน เช่น checkout, lint, build, validate
- **steps**: เป็นขั้นตอนย่อยภายใน job แต่ละ step จะทำงานตามลำดับ ประกอบด้วย name, uses (สำหรับ action), หรือ run (สำหรับ command)

---

### โจทย์ที่ 2: Docker Compose YAML

**คำถาม 2.1:** อธิบายว่า `services`, `ports`, `volumes`, `environment` ใช้ทำอะไร

**คำตอบ:** 
- **services**: กำหนด container ต่างๆ ที่ต้องการรัน เช่น web service (Nginx) และ db service (MySQL)
- **ports**: แมป port จาก host machine เข้าสู่ container เช่น "8080:80" หมายถึงเข้าถึง nginx ผ่าน localhost:8080
- **volumes**: แชร์ไฟล์และโฟลเดอร์ระหว่าง host และ container เพื่อเก็บข้อมูลแบบถาวรและแชร์ไฟล์
- **environment**: กำหนดตัวแปรสิ่งแวดล้อมสำหรับ container เช่น รหัสผ่าน database หรือ configuration

**คำถาม 2.2:** อธิบายว่า Docker Compose ทำงานร่วมกับ Project ยังไง

**คำตอบ:** 
Docker Compose ช่วยจัดการ multiple containers ในโปรเจค โดยสามารถรัน web server และ database พร้อมกันด้วยคำสั่งเดียว ทำให้ developer สามารถ setup environment ได้ง่าย และ CI/CD pipeline สามารถใช้ทดสอบ integration ระหว่าง services ต่างๆ

---

### โจทย์ที่ 3: Kubernetes YAML

**คำถาม 3.1:** อธิบายว่า `Deployment`, `replicas`, `selector`, `template` คืออะไร

**คำตอบ:** 
- **Deployment**: จัดการการ deploy และ update applications บน Kubernetes cluster
- **replicas**: จำนวน Pod ที่ต้องการให้ทำงาน (ในที่นี้คือ 2 ตัว) เพื่อ high availability
- **selector**: เลือก Pod ที่ Deployment จะจัดการโดยใช้ label matching
- **template**: แม่แบบสำหรับสร้าง Pod ใหม่ที่มี specification เดียวกัน

**คำถาม 3.2:** อธิบายว่า `Service`, `port`, `targetPort` ทำงานยังไง

**คำตอบ:** 
- **Service**: ทำหน้าที่เป็น load balancer และ network endpoint สำหรับเข้าถึง Pods
- **port**: port ที่ Service เปิดให้ภายใน cluster (80)
- **targetPort**: port ที่ container ภายใน Pod เปิดรับ (80)
- **nodePort**: port ที่เปิดให้เข้าถึงจากภายนอก cluster (30080)

**คำถาม 3.3:** อธิบายความสัมพันธ์ระหว่าง Deployment และ Service

**คำตอบ:** 
Deployment สร้างและจัดการ Pods พร้อม labels, ส่วน Service ใช้ selector เพื่อหา Pods ที่มี label ตรงกันแล้วกระจาย traffic ไปยัง Pods เหล่านั้น Service ทำหน้าที่เป็น stable network interface ให้กับ Pods ที่ Deployment จัดการ

---

### โจทย์ที่ 4: รวม YAML หลายไฟล์

**คำถาม 4.1:** อธิบายว่าแต่ละไฟล์ YAML ทำงานร่วมกันอย่างไร

**คำตอบ:** 
- **ci.yml**: ตรวจสอบและ validate ไฟล์ YAML อื่นๆ ทั้งหมด
- **docker-compose.yml**: ใช้สำหรับ development และ testing environment
- **k8s/*.yml**: ใช้สำหรับ production deployment ที่ต้องการ scalability
ทั้งหมดทำงานร่วมกันเป็น pipeline ที่สมบูรณ์

**คำถาม 4.2:** อธิบายลำดับขั้นตอนเมื่อ Push โค้ดไปที่ GitHub (Workflow → Docker → Kubernetes)

**คำตอบ:** 
1. Push โค้ด → GitHub Actions trigger
2. CI Pipeline: checkout → lint YAML → validate Docker Compose → test K8s dry-run → build
3. หาก tests ผ่าน → พร้อม deploy
4. Development: ใช้ Docker Compose
5. Production: ใช้ Kubernetes deployment

**คำถาม 4.3:** วาด Diagram แสดงความสัมพันธ์ระหว่าง CI/CD Pipeline, Docker Compose และ Kubernetes

**คำตอบ:** 
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Developer     │    │   GitHub Actions │    │   Deployment    │
│   Push Code     │───▶│   CI/CD Pipeline │───▶│   Environment   │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                                ▼                        ▼
                       ┌──────────────────┐    ┌─────────────────┐
                       │   Validation:    │    │   Target Env:   │
                       │   • YAML Lint    │    │                 │
                       │   • Docker Test  │    │ ┌─────────────┐ │
                       │   • K8s Dry-run  │    │ │Docker Compose│ │
                       └──────────────────┘    │ │(Development)│ │
                                               │ └─────────────┘ │
                                               │ ┌─────────────┐ │
                                               │ │ Kubernetes  │ │
                                               │ │(Production) │ │
                                               │ └─────────────┘ │
                                               └─────────────────┘
```

---

## 📖 รายละเอียดแต่ละไฟล์

### 1. GitHub Actions Workflow (`.github/workflows/ci.yml`)

#### ทำไมต้องใช้ `on: push` และ `on: pull_request`?
- **`on: push`**: ทำงานเมื่อมีการ push โค้ดไปยัง branch main เพื่อตรวจสอบโค้ดใหม่ทันที
- **`on: pull_request`**: ทำงานเมื่อมีการสร้าง PR เพื่อตรวจสอบความถูกต้องก่อน merge
- **เหตุผล**: ป้องกันโค้ดที่มีปัญหาเข้าสู่ branch หลัก และให้ feedback เร็วแก่ developer

#### jobs และ steps ทำหน้าที่อะไร?
- **jobs**: กลุ่มของงานที่ทำงานแยกกัน สามารถทำงานพร้อมกันหรือต่อเนื่องกัน
  - `checkout`: ดึงโค้ดจาก repository
  - `lint`: ตรวจสอบ syntax ของ YAML files
  - `build`: สร้าง application
  - `docker-validate`: ตรวจสอบ Docker Compose
  - `k8s-validate`: ตรวจสอบ Kubernetes manifests
  - `notify`: แจ้งผลการทำงาน
- **steps**: ขั้นตอนย่อยภายใน job แต่ละ step ทำงานตามลำดับ

### 2. Docker Compose (docker-compose.yml)

#### services, ports, volumes, environment ใช้ทำอะไร?
- **services**: กำหนด container ต่างๆ ที่ต้องการรัน
  - `web`: Nginx web server
  - `db`: MySQL database
- **ports**: แมป port จาก host เข้าสู่ container
  - `"8080:80"`: เข้าถึง nginx ผ่าน localhost:8080
  - `"3306:3306"`: เข้าถึง MySQL ผ่าน port 3306
- **volumes**: แชร์ไฟล์ระหว่าง host และ container
  - `./html:/usr/share/nginx/html`: แชร์ไฟล์ HTML
  - `mysql_data:/var/lib/mysql`: เก็บข้อมูล MySQL แบบถาวร
- **environment**: กำหนดตัวแปรสิ่งแวดล้อม
  - `MYSQL_ROOT_PASSWORD`: รหัสผ่าน root ของ MySQL
  - `MYSQL_DATABASE`: ชื่อ database เริ่มต้น

#### Docker Compose ทำงานร่วมกับ Project ยังไง?
- **Development**: ใช้รัน environment ในเครื่อง developer
- **Testing**: CI/CD pipeline ใช้ตรวจสอบว่า services ทำงานร่วมกันได้
- **Production**: สามารถ deploy ด้วย Docker Swarm หรือปรับแต่งสำหรับ production

### 3. Kubernetes YAML

#### Deployment, replicas, selector, template คืออะไร?
- **Deployment**: จัดการการ deploy และ update applications
- **replicas**: จำนวน Pod ที่ต้องการให้ทำงาน (2 ตัว)
- **selector**: เลือก Pod ที่ Deployment จะจัดการ (ตาม label `app: nginx`)
- **template**: แม่แบบสำหรับสร้าง Pod ใหม่

#### Service, port, targetPort ทำงานยังไง?
- **Service**: เป็น load balancer ภายใน cluster เพื่อเข้าถึง Pods
- **port**: port ที่ Service เปิดให้ภายใน cluster (80)
- **targetPort**: port ที่ container เปิดรับ (80)
- **nodePort**: port ที่เปิดให้เข้าถึงจากภายนอก cluster (30080)

#### ความสัมพันธ์ระหว่าง Deployment และ Service
```
External Traffic (NodePort:30080) 
    ↓
Service (nginx-service:80) 
    ↓ 
Pods (nginx:80) created by Deployment
```
- Service ใช้ selector `app: nginx` เพื่อหา Pods ที่สร้างโดย Deployment
- Deployment สร้าง Pods พร้อม label `app: nginx`
- Service กระจาย traffic ไปยัง Pods ทั้งหมด

## 🔄 Integration: YAML ไฟล์ทำงานร่วมกันอย่างไร

### ลำดับขั้นตอนเมื่อ Push โค้ดไปที่ GitHub:

1. **GitHub Actions Trigger** (ci.yml)
   ```
   Developer pushes code → GitHub Actions starts
   ```

2. **Workflow Execution**
   ```
   Checkout → Lint YAML → Build → Validate Docker → Validate K8s → Notify
   ```

3. **Docker Integration**
   ```
   CI validates docker-compose.yml → Ensures services can start
   ```

4. **Kubernetes Integration**
   ```
   CI validates k8s/*.yml → Ensures manifests are deployable
   ```

### Deployment Pipeline Flow:
```
Code Push → CI/CD Pipeline → Docker Build → K8s Deploy
     ↓              ↓              ↓           ↓
   ci.yml    Validate YAML    docker-compose  k8s/*.yml
```

##  สรุป Benefits

1. **Automation**: ทุกอย่างทำงานอัตโนมัติเมื่อ push โค้ด
2. **Validation**: ตรวจสอบ YAML ทุกไฟล์ก่อน deploy
3. **Consistency**: ใช้ configuration เดียวกันทุก environment
4. **Scalability**: K8s รองรับการ scale แบบอัตโนมัติ
5. **Monitoring**: CI/CD แจ้งเตือนเมื่อมีปัญหา

---

## 🎯 การศึกษาต่อ

- ลองเพิ่ม tests ใน CI pipeline
- ศึกษา Helm Charts สำหรับ K8s
- เพิ่ม monitoring ด้วย Prometheus/Grafana
- ใช้ GitOps ด้วย ArgoCD