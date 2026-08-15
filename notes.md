# AWS (Amazon Web Services)

Amazon Web Services (AWS) ek cloud computing platform hai jo Amazon provide karta hai. Ismein compute, storage, database, networking, AI/ML, aur 200+ cloud services available hain jo pay-as-you-go model pe milti hain.

## Route 53

AWS Route 53 ek **DNS (Domain Name System)** web service hai jo domain names (jaise amazon.com) ko IP addresses me resolve karta hai. Iska naam "Route 53" DNS ke port 53 se aaya hai.

**Example - amazon.com:**
Jab aap browser me `amazon.com` type karte hain:
1. Browser Route 53 se puchta hai ki "amazon.com ka IP kya hai?"
2. Route 53 DNS records check karta hai
3. Aapko Amazon ke server ke IP address (jaise `176.32.103.205`) pe bhej deta hai
4. Aapka browser us IP se connect hokar website load karta hai

Route 53 main features:
- **Domain registration** - naye domain khareedna (jaise amazon.com)
- **DNS routing** - domain ko IP se map karna
- **Health checking** - servers healthy hain ya nahi check karna

### Route 53 Setup Steps

1. **Domain register karo** (ya existing domain use karo)
   - AWS Route 53 se naya domain khareedo ya kisi aur registrar se laao

2. **Hosted Zone banao**
   - AWS Console → Route 53 → Hosted Zones → "Create Hosted Zone"
   - Domain name daalo (jaise `example.com`) aur type "Public Hosted Zone" select karo

3. **DNS records add karo**
   - **A record** - domain ko IPv4 address se map karo (jaise `example.com → 192.0.2.1`)
   - **CNAME record** - subdomain ko dusre domain se map karo (jaise `www.example.com → example.com`)
   - **MX record** - email ke liye
   - **NS record** - name server details (ye automatically aa jate hain)

4. **Name servers update karo** (agar domain bahar se khareeda hai)
   - Route 53 4 name server addresses deta hai
   - Apne domain registrar ke paas jaake NS records update karo

5. **Health checks configure karo** (optional)
   - Resources healthy hain ya nahi monitor karne ke liye

6. **Test karo**
   - `dig example.com` ya `nslookup example.com` command se check karo

## S3 (Simple Storage Service)

AWS S3 ek **object storage** service hai jo cloud me unlimited data store karne deti hai. Data **buckets** me save hota hai aur har file (object) ka ek unique URL hota hai.

**Example - amazon.com ke product images:**
Jab aap amazon.com koi product kholte hain to images dikhti hain:
1. Amazon ke servers product images ko S3 buckets me store karte hain
2. Har image ka ek URL hota hai jaise: `https://m.media-amazon.com/images/I/71eX.jpg`
3. Jab aap page kholte hain, browser directly S3 se image fetch karta hai

**Example - amazon.com ka static content:**
- Product photos, CSS files, JavaScript files — sab S3 me store hote hain
- Amazon Static CDN bhi S3 ke upar banaya hai (amazon.com/images/, amazon.com/css/)
- Ye high traffic handle kar leta hai bina server load kiye

**S3 key features:**
- **Scalability** - kitni bhi storage, automatically scale ho jata hai
- **Durability (99.999999999%)** - data 11 9s tak safe rehta hai
- **Security** - IAM polices, bucket policies, encryption
- **Lifecycle policies** - old data ko cheaper storage me move karna (S3 → S3 Glacier)
- **Static website hosting** - S3 se directly website host kar sakte hain

**Storage Classes:**
- S3 Standard - frequently accessed data
- S3 Infrequent Access - kam access hone wala data
- S3 Glacier - archival data (backup ke liye)
- S3 Glacier Deep Archive - sabse sasta

### S3 Setup Steps

1. **AWS Console me login karo** → S3 service me jao → "Create bucket" click karo

2. **Create bucket form me ye settings hain:**

   **Bucket type:**
   - **General purpose** - normal use cases, multiple AZs, mix of storage classes (recommended)
   - **Directory** - low latency, single AZ, only S3 Express One Zone

   **Bucket namespace:**
   - **Global namespace** - default, bucket name world-wide unique
   - **Account Regional namespace (recommended)** - unique to your account, kisi aur ka same name ho sakta hai

   **Bucket name:** globally unique naam (3-63 chars, a-z 0-9 . -)

   **Object Ownership:**
   - **ACLs disabled (recommended)** - sab objects is account ke, sirf policies se access control
   - **ACLs enabled** - doosre AWS accounts bhi objects ke owner ho sakte hain

   **Block Public Access settings (4 options):**
   - Block public access to buckets and objects granted through **new ACLs**
   - Block public access to buckets and objects granted through **any ACLs**
   - Block public access through **new public bucket or access point policies**
   - Block **public and cross-account access** through any public bucket/access point policies
   - ⚡ "Block all public access" ON rakho unless website host karna ho

   **Bucket Versioning:**
   - **Disable** (default)
   - **Enable** - multiple object versions rakhna, accidental delete se bachao

   **Tags - optional:** cost allocation aur resource organization ke liye

3. **Objects (files) upload karo**
   - Bucket kholo → "Upload" click karo
   - Files ya folders drag & drop karo
   - Upload ho jane par har file ka ek **object URL** milta hai

4. **Permissions set karo**
   - **Bucket Policy** - JSON policy likho ki kon access kar sakta hai
   - **IAM Policies** - users/roles ko S3 access do
   - **ACLs** - object-level permissions (optional)

5. **Versioning enable karo** (recommended)
   - "Properties" tab → "Bucket Versioning" → "Enable"
   - Har update par purani file version save rehti hai — accidental delete se bachao

6. **Static website hosting** (agar website host karna hai)
   - "Properties" → "Static website hosting" → "Enable"
   - Index document (jaise `index.html`) set karo
   - Bucket public karo aur content upload karo — ek live website ready!

7. **Lifecycle rules banao** (cost save karne ke liye)
   - 30 din baad → S3 Standard se S3 Infrequent Access
   - 90 din baad → S3 Glacier
   - 365 din baad → permanently delete

8. **Test karo**
   - AWS Console me file kholo ya CLI se check karo:
   - `aws s3 ls s3://my-bucket-name`
   - `aws s3 cp test.txt s3://my-bucket-name/`

### S3 Object - Terminal me URL nikalne ke tarike

S3 me kisi object ko reference karne ke 3 tarike hain:

| Type | Example |
|------|---------|
| **S3 URI** | `s3://werdfgas34/about-us.png` |
| **ARN** | `arn:aws:s3:::werdfgas34/about-us.png` |
| **Object URL** | `https://werdfgas34.s3.us-east-1.amazonaws.com/about-us.png` |

**Terminal se Object URL generate karna:**
```bash
# Presigned URL (temporary access — public bucket nahi hai to bhi kaam karta hai)
aws s3 presign s3://werdfgas34/about-us.png --expires-in 3600

# S3 URI format se file copy karna
aws s3 cp s3://werdfgas34/about-us.png ./downloads/

# Object ka info dekhna (Etag, size, last modified)
aws s3api head-object --bucket werdfgas34 --key about-us.png

# Object URL manually banao (public bucket ho tab)
echo "https://werdfgas34.s3.us-east-1.amazonaws.com/about-us.png"
```

**Pattern:**
```
https://<bucket-name>.s3.<region>.amazonaws.com/<key>
```

**Presigned URL** — jab bucket private ho aur kisi ko temporary access dena ho (like shareable link).

### ⚠️ Access Denied — kyun aur kya karein?

**Reason:** Bucket private hai (Block Public Access ON hai). Direct Object URL se sirf **public buckets** ka hi access hota hai.

**⚠️ Pehle AWS CLI install karo:**
```bash
sudo apt update && sudo apt install awscli -y
# Verify:
aws --version
```
AWS CLI install hone ke baad **configure** bhi karna zaroori hai:
```bash
aws configure
# Enter:
#   AWS Access Key ID
#   AWS Secret Access Key
#   Default region (jaise us-east-1)
#   Default output format (json/table/text)
```

**Solution 1 — Presigned URL (recommended):**
```bash
# Terminal se presigned URL generate karo
aws s3 presign s3://werdfgas34/about-us.png --expires-in 3600
# Output: https://werdfgas34.s3.us-east-1.amazonaws.com/about-us.png?AWSAccessKeyId=...&Signature=...&Expires=...
# Ye URL 1 ghante (3600 sec) ke liye valid rahega
```

**Solution 2 — File copy karo:**
```bash
aws s3 cp s3://werdfgas34/about-us.png ./
```

**Solution 3 — Bucket/S3 ko public karo (risky):**
- Console → bucket → Permissions → Block Public Access → OFF karo
- Bucket Policy me public read permission do:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::werdfgas34/*"
  }]
}
```
⚠️ Public karne se koi bhi object access kar sakta hai — sirf static websites ya public files ke liye karo.

## CloudFront (CDN)

AWS CloudFront ek **Content Delivery Network (CDN)** service hai jo static/dynamic content ko **Edge locations** (duniya bhar ke servers) se serve karta hai. Ye user ke nearest location se content deliver karta hai — jisse **latency** (speed) improve hoti hai.

**Example — amazon.com:**
Jab aap amazon.com kholte hain:
1. Amazon ka content (images, CSS, JS, product pages) CloudFront pe deploy hai
2. Agar aap India me hain to CloudFront **Mumbai Edge location** se serve karega
3. User in US ko **Virginia ya Oregon** Edge se serve hoga
4. Jo content already cached hai wo turant milta hai, jo nahi hai wo origin (S3/EC2) se fetch ho k cache ho jata hai

**CloudFront vs S3 direct:**
| Feature | S3 Direct | CloudFront + S3 |
|---------|-----------|----------------|
| Speed | Sirf us region se | Nearest edge se worldwide |
| Latency | High (door ke users ke liye) | Low (edge location se) |
| Cost | Data transfer charges | Thoda zyada but faster |
| DDoS protection | Limited | Built-in (AWS Shield) |
| Caching | ❌ | ✅ (edge pe cache karta hai) |

**Key features:**
- **Edge locations** — 600+ points of presence worldwide
- **Origin** — S3, EC2, ALB, ya external server ho sakta hai
- **Caching** — frequently accessed content edge pe store hota hai
- **TTL** — cache kitni der valid rahega (sec/min/hours)
- **Geo-restriction** — specific countries ko allow/block karna
- **Custom SSL** — apna domain aur SSL certificate (CloudFront se HTTPS)
- **DDoS protection** — AWS Shield + AWS WAF integrated
- **Lambda@Edge** — edge location pe code run karna (like URL rewrite, auth)

**Behaviour:**
- User request → nearest edge location → agar cached hai to serve karo
- Agar cached nahi hai → origin se fetch karo → cache karo → user ko do

**Price:**
- Data transfer out (per GB)
- Request count (HTTP/HTTPS)
- Regional pricing (India/SA/US — alag alag rates)

## Load Balancer (ELB - Elastic Load Balancer)

AWS Load Balancer ek **traffic distributer** hai jo incoming requests ko multiple servers (EC2 instances) me automatically distribute karta hai.

**Example — amazon.com:**
Amazon ke lakhon users ek saath aate hain:
1. Ek server sab handle nahi kar sakta — isliye Amazon ke **1000+ servers** hain
2. Load Balancer sab requests leta hai aur **healthy servers** ko bhejta hai
3. Agar ek server fail ho jaye to Load Balancer automatically usko hatakar doosre server pe request bhejta hai
4. User ko pata bhi nahi chalta — ek hi server se baat kar rahe hain jaise lagta hai

**Load Balancer kyun chahiye?**
- **High traffic handle** — ek server pe load nahi aane deta
- **High availability** — ek server down ho to doosra sambhal leta hai
- **Fault tolerance** — failed instances ko hatakar naye healthy instances pe bhejta hai
- **Auto Scaling** — naye EC2 instances aate hi automatically unme bhi traffic bhejne lagta hai
- **Health checks** — har server ko ping karta hai, jo fail ho usko rotation se hata deta hai

**Types of Load Balancers:**

| Type | Protocol | Use Case |
|------|----------|----------|
| **ALB (Application LB)** | HTTP/HTTPS/gRPC | Web apps, microservices, path-based routing |
| **NLB (Network LB)** | TCP/UDP | High performance, low latency, static IP |
| **GWLB (Gateway LB)** | IP | Third-party appliances (firewalls, IDS/IPS) |

**ALB vs NLB — kab kya use karein?**

| Feature | ALB | NLB |
|---------|-----|-----|
| Layer | Layer 7 (Application) | Layer 4 (Transport) |
| Content-based routing | ✅ (path, host, headers) | ❌ |
| Path routing (`/api/*`, `/images/*`) | ✅ | ❌ |
| Host routing (`api.example.com`) | ✅ | ❌ |
| Static IP | ❌ | ✅ (Elastic IP assign) |
| Latency | Slight delay (reads headers) | Minimal (high speed) |
| WebSockets | ✅ | ❌ |
| gRPC | ✅ | ❌ |
| TLS termination | ✅ | ✅ |
| Target groups | EC2, Lambda, IP, ALB | EC2, IP, ALB |

**Components:**

- **Listener** — Load Balancer ka "ear" — ek port aur protocol sunta hai (jaise HTTP:80, HTTPS:443)
- **Target Group** — servers ka group jahan traffic bhejna hai (jaise 5 EC2 instances)
- **Health Check** — har `n` seconds pe target group ke instances ko ping karta hai
- **Rules** — ALB me decide karta hai kaunsi request kahan jayegi (path/host based)

**Example setup — Web app with ALB:**

```
Users → ALB (HTTPS:443) → Target Group (EC2 instances)
                              ├── EC2 #1 (Healthy)
                              ├── EC2 #2 (Healthy)
                              └── EC2 #3 (Unhealthy → removed)
```

**ALB Routing Rules — real-world example:**

| Rule | Condition | Action |
|------|-----------|--------|
| 1 | IF path starts with `/api/*` | Forward to **API Target Group** |
| 2 | IF host is `images.example.com` | Forward to **Image Server TG** |
| 3 | IF path is `/login` | Forward to **Auth TG** |
| 4 | ELSE (default) | Forward to **Web App TG** |

**Sticky Sessions (Session Affinity):**
- Load Balancer normally requests randomly distribute karta hai
- Sticky session ON karne par ek user ki **saari requests same server** pe jaati hain
- Cookie-based kaam karta hai — ALB ek cookie set karta hai aur ussi server pe bhejta rehta hai
- Use case: Shopping cart — user ka cart jis server pe bana, wahi server handle kare

**Cross-Zone Load Balancing:**
- Normally ALB sirf apne AZ ke instances ko traffic bhejta hai
- Cross-zone ON karne par ALB **saare AZs** ke instances me equally distribute karta hai
- Better utilization — ek AZ me 10 instances aur doosre me 2 hain to bhi load barabar batega

**Connection Draining (Deregistration Delay):**
- Jab EC2 instance deregister/down ho raha ho to Load Balancer usko **turant nahi hatata**
- Existing connections ko khatam hone ka time deta hai (default 300 sec)
- Nayi requests nahi aayengi, purani puri ho jayengi — **zero downtime**

**Setup Steps (ALB):**

1. **Target Group banao**
   - AWS Console → EC2 → Target Groups → "Create target group"
   - Target type: Instances / IP / Lambda
   - Protocol: HTTP, port: 80
   - Health check path: `/health` (ya `/`)
   - Healthy threshold: 2 (2 success = healthy)
   - Unhealthy threshold: 2 (2 fail = unhealthy)
   - Register EC2 instances isme

2. **Load Balancer banao**
   - EC2 → Load Balancers → "Create Load Balancer" → ALB select karo
   - Scheme: **Internet-facing** (public) ya **Internal** (private VPC)
   - IP address type: IPv4 ya Dualstack (IPv4 + IPv6)
   - VPC aur subnets select karo (at least 2 AZs)
   - Security group assign karo (allow HTTP/HTTPS)

3. **Listener configure karo**
   - HTTP:80 ya HTTPS:443
   - Default action → Target group forward karo

4. **Route 53 me DNS add karo**
   - ALB ka DNS name hota hai (jaise `my-alb-12345.us-east-1.elb.amazonaws.com`)
   - Route 53 me A record banao → **Alias** select karo → ALB select karo
   - Ab `example.com` ALB pe point karega

5. **Auto Scaling group attach karo** (optional)
   - Auto Scaling group ke saath ALB attach kar sakte hain
   - Naye instances aate hi ALB automatically unme traffic bhejta hai
   - Old instances jate hi ALB hatata hai

**Important Points:**
- ALB ka **fixed DNS name** hota hai, IP nahi — isliye Route 53 me Alias record use karo
- ALB **public subnet** me hota hai (internet-facing) ya **private subnet** me (internal)
- Load Balancer ke saath **Auto Scaling** + **Multi-AZ** best practice hai
- SSL certificate ALB pe terminate karo (ALB se EC2 tak HTTP bhej sakte hain)

## API Gateway

AWS API Gateway ek **fully managed API service** hai jo developers ke liye REST/WebSocket APIs create, publish, maintain, monitor aur secure karna easy banati hai. Ye **serverless** hai — aapko koi server manage nahi karna padta.

**Example — amazon.com app:**
Amazon ka mobile app jab kuch data mangata hai:
1. App API Gateway ko request bhejta hai: "mujhe product details chahiye"
2. API Gateway request validate karta hai, rate limit check karta hai
3. Fir request ko **Lambda function** (ya EC2/any backend) pe forward karta hai
4. Lambda response deta hai → API Gateway usse format karta hai → app ko bhejta hai

**API Gateway vs Load Balancer — difference:**

| Feature | API Gateway | Load Balancer (ALB) |
|---------|-------------|-------------------|
| Layer | API management | Traffic distribution |
| Protocol | REST, WebSocket, HTTP | HTTP, TCP, UDP, gRPC |
| Rate limiting | ✅ | ❌ (ALB nahi karta) |
| API keys / Usage plans | ✅ | ❌ |
| Request/Response transformation | ✅ | ❌ |
| Caching | ✅ | ❌ (CloudFront ke saath) |
| Authentication (Cognito, JWT, Lambda authorizer) | ✅ | ❌ (ALB bhi auth kar sakta hai via OIDC) |
| WAF integration | ✅ | ✅ |
| SDK generation | ✅ | ❌ |
| Throttling | ✅ | ❌ |
| Monitoring (per-API metrics) | ✅ | ❌ (basic metrics) |

**API Gateway use cases:**
- **Serverless APIs** — Lambda ke saath REST API banao — koi server nahi, auto-scale
- **API proxy** — existing backend (EC2, on-prem) ke aage proxy laga do — rate limit, auth, caching
- **WebSocket APIs** — real-time apps (chat, notifications, live streaming)
- **API monetization** — API keys + usage plans → developers ko API becho

**API Gateway types:**

| Type | Use Case |
|------|----------|
| **REST API** | Traditional REST APIs, full control over request/response |
| **HTTP API** | Simple, low-latency, cheaper — Lambda/HTTP backends ke liye |
| **WebSocket API** | Real-time bidirectional communication |

**HTTP API vs REST API — kab kya?**

| Feature | HTTP API | REST API |
|---------|----------|----------|
| Price | ~50% cheaper | Zyada |
| Latency | Lower | Slightly higher |
| Custom domain | ✅ | ✅ |
| API keys / Usage plans | ❌ | ✅ |
| Request validation | Basic | Advanced |
| WAF | ✅ | ✅ |
| Lambda authorizer | ✅ | ✅ |
| Cognito | ✅ | ✅ |
| API Gateway caching | ❌ | ✅ |

**Components:**

- **API** — aapki API definition (endpoints, methods, integrations)
- **Resource** — URL path (jaise `/products`, `/users/{id}`)
- **Method** — GET, POST, PUT, DELETE, PATCH
- **Integration** — backend se connect karna:
  - **Lambda function** (serverless)
  - **HTTP** (EC2, ALB, any HTTP endpoint)
  - **AWS service** (SQS, DynamoDB, Step Functions, Kinesis)
  - **VPC Link** (private VPC resources)
  - **Mock** (test response bina backend ke)
- **Stage** — deployment stage (dev, test, prod — alag URLs milte hain)
- **API Key** — developer ko API access dene ke liye
- **Usage Plan** — rate limit aur quota set karna (1000 req/hour)
- **Authorizer** — authentication (Cognito, JWT, Lambda)

**Example — Serverless REST API setup:**

```
Mobile App / Web
     ↓
API Gateway (REST API)
     ↓  (Lambda integration)
Lambda Function (business logic)
     ↓
DynamoDB (database)
```

**API Gateway endpoints:**

1. **Edge-optimized** — CloudFront ke through serve hota hai, worldwide users ke liye best
2. **Regional** — ek region me serve hota hai, apna CDN khud banao (CloudFront manually)
3. **Private** — sirf VPC ke andar accessible, public internet se nahi

**Rate limiting aur throttling:**
- **Per-client throttle** — har API key ke liye limit (jaise 100 req/sec)
- **Account-level throttle** — total API Gateway usage limit (jaise 10,000 req/sec)
- **Burst limit** — sudden traffic spike handle karne ke liye extra capacity (jaise 5000 req/sec burst)
- **429 Too Many Requests** — limit cross karne par ye error aata hai

**Caching:**
- API Gateway **TTL-based caching** support karta hai (default 300 sec)
- Cache hit → backend call nahi hoti, directly response milta hai
- Cache size: 500MB to 237GB
- Cache individual endpoints, query parameters ke saath bhi

**Setup steps — REST API with Lambda:**

1. **Lambda function banao**
   - AWS Console → Lambda → "Create function"
   - Runtime: Node.js, Python, etc.
   - Code likho jo event.body process kare aur response return kare

2. **API Gateway banao**
   - API Gateway → "Create API" → REST API → "Build"
   - Protocol: REST
   - "New API" select karo, name do

3. **Resource aur Method banao**
   - "Create Resource" → path daalo (jaise `/users`)
   - "/users" select karo → "Create Method" → GET
   - Integration type: Lambda Function
   - Lambda function select karo

4. **Request/Response transform karo** (optional)
   - **Mapping templates** — backend ke response ko API client ke format me badlo
   - Jaise: Lambda se `{statusCode: 200, body: "..."}` aata hai, API Gateway sirf body bhejta hai

5. **CORS enable karo** (web apps ke liye zaroori)
   - "Enable CORS" → allow origins, methods, headers
   - OPTIONS method automatically create ho jata hai

6. **Deploy karo**
   - "Deploy API" → naya stage banao (jaise `prod`)
   - Invoke URL milta hai: `https://abc123.execute-api.us-east-1.amazonaws.com/prod/users`
   - URL directly browser ya app me use kar sakte ho

7. **Custom domain aur SSL lagaao** (production ke liye)
   - Route 53 me domain point karo
   - API Gateway → Custom Domain → Certificate lagaao
   - `https://api.example.com/prod/users` — ab aisa dikhega

8. **API Key aur Usage Plan banao** (optional)
   - Usage Plan banao → rate limit (100 req/sec), quota (10000 req/day)
   - API Key generate karo → usage plan se attach karo
   - Developers ko API key do → wo request me `x-api-key` header bhejenge
   - API Gateway key validate karega, limit cross hui to 429 dega

**Important Points:**
- API Gateway ka **default timeout 29 seconds** hai — longer operations ke liye async pattern use karo
- Request body max size **10MB** (REST), **4MB** (HTTP API)
- Lambda cold start + API Gateway latency ka **dhyan rakho** — warmup strategy use kar sakte ho
- **Edge-optimized** API endpoint automatic CloudFront use karta hai — alag se CDN nahi lagna padta
- API Gateway pe **WAF attach** kar sakte ho — SQL injection, XSS, DDoS protection
- `$default` stage — default URL pe requests kisi bhi URL path se aa sakti hain (catch-all)

## AWS WAF (Web Application Firewall)

WAF ek **firewall** hai jo aapki web app ko **common web attacks** se bachata hai — jaise SQL injection, XSS, aur bad bots.

**Example — Amazon checkout page:**
Koi hacker aapke checkout page pe SQL injection try karta hai:
```
POST /checkout  body: "user_id=1; DROP TABLE orders"
```
1. Request pehle **WAF** pe aati hai
2. WAF check karta hai — "yeh SQL injection dikhta hai"
3. WAF request **block** kar deta hai
4. Hacker ko **403 Forbidden** error milta hai
5. Aapka backend safe rehta hai

**WAF kaise kaam karta hai?**
- Aap **rules** banate ho — "kya allow karna hai, kya block karna hai"
- WAF har incoming request ko in rules se match karta hai
- Match mila to → Allow / Block / Count (sirf count karo, block mat karo)

**Common WAF Rules:**
| Rule | Kya karta hai |
|------|--------------|
| SQL Injection | Request me SQL code hai to block |
| XSS (Cross-Site Scripting) | Request me malicious JS hai to block |
| IP rate limiting | Ek IP se 100 req/sec se zyada aaye to block |
| Geo-block | Specific countries se request block |
| Bad bots | Scrapers, crawlers ko block |
| Size limit | Request body 10KB se bada ho to block |

**WAF kahan lagate hain?**
- **CloudFront** ke saath (most common)
- **ALB** ke saath
- **API Gateway** ke saath
- **AppSync** ke saath

**WAF vs ALB Security Group:**

| Feature | Security Group | WAF |
|---------|---------------|-----|
| Layer | Network (Layer 3/4) | Application (Layer 7) |
| Block by | IP, port, protocol | SQL injection, XSS, bot, URI path |
| Analogy | **Gatekeeper** — kaun andar aa sakta hai | **Bouncer** — andar aane ke baad kya kar sakta hai |

## AWS Shield

Shield ek **DDoS protection** service hai jo aapki app ko **DDoS attacks** se bachati hai. DDoS = jab hacker lakhons fake requests bhej kar server ko crash karne ki koshish kare.

**Example — Amazon par DDoS attack:**
Koi hacker group Amazon par attack karta hai:
1. 1,00,000 hacked computers ek saath Amazon ko request bhejna shuru karte hain
2. **Shield** automatically attack detect karta hai — "yeh normal traffic nahi hai"
3. Shield malicious traffic **filter out** karta hai
4. Real users ka traffic normally flow karta rehta hai
5. Amazon ka server **down nahi hota**, sab normal chalta hai
6. Hacker fail ho jata hai

**Shield — 2 versions:**

| Feature | Shield Standard (Free) | Shield Advanced ($3000/month) |
|---------|----------------------|------------------------------|
| Price | **Free** — automatically on | Paid + 1 year commit |
| DDoS protection | Basic (Layer 3/4) | Advanced (Layer 3/4/7) |
| Coverage | Route 53, CloudFront | Route 53, CloudFront, ALB, NLB, EC2, Global Accelerator |
| 24/7 support | ❌ | ✅ (DDoS Response Team - DRT) |
| Cost protection | ❌ | ✅ (attack ke time scale hua to AWS bill waive karta hai) |
| Real-time visibility | Basic | Advanced (CloudWatch metrics, attack logs) |

**Shield Standard (Free) kya karta hai:**
- Har AWS account ko automatically milta hai
- Route 53 aur CloudFront pe basic DDoS protection
- Network layer attacks se bachata hai (SYN floods, UDP floods)
- Kuch configure nahi karna padta — **auto ON**

**Shield Advanced kab lo?**
- Production app hai jiska downtime **costly** hai
- Pehle DDoS attack aa chuka hai
- 24/7 AWS expert support chahiye (DRT)
- EC2, ALB, NLB ko bhi protect karna hai

**WAF + Shield — real-world scenario:**

```
Internet
   ↓
Shield (DDoS protection — bad traffic filter)
   ↓
WAF (SQL injection, XSS, bots block)
   ↓
CloudFront / ALB / API Gateway
   ↓
Backend (EC2 / Lambda)
```

**Analogy:**
- **Shield** = **Gate security** — bahar se aane wale **huge crowd** ko control karta hai
- **WAF** = **Security check post** — andar aane ke baad **kisne kya weapon** chhupa rakha hai check karta hai

**Simple difference:**
| Service | Kya attack rokta hai? |
|---------|----------------------|
| Shield | **Volume attack** — lakhon fake requests jo server ko overload karein |
| WAF | **Application attack** — SQL injection, XSS jo data chura sake |

## ACM (AWS Certificate Manager)

ACM ek **free SSL/TLS certificate** dene wali service hai jo AWS services ke saath automatically integrate ho jati hai. Aapko manually certificate buy, install ya renew nahi karna padta.

**Example — Amazon pe HTTPS:**
Jab aap `https://amazon.com` kholte hain:
1. Browser check karta hai — "kya ye site secure hai?"
2. Amazon ka SSL certificate browser ko batata hai — "haan, main genuine Amazon hoon"
3. Aapke browser aur Amazon ke beech **encrypted connection** banta hai
4. Koi hacker beech me data nahi padh sakta
5. Ye certificate **ACM** jaise service se aata hai

**Example — apni website pe HTTPS lagaana:**
Maano aapne `shop.example.com` ek ALB pe host kiya hai:
1. **Pehle:** Users ko `http://shop.example.com` pe chodna padta (Not Secure dikhta hai)
2. **ACM me:** `shop.example.com` ka SSL certificate request karo
3. **ALB me:** Certificate attach karo
4. **Ab:** Users `https://shop.example.com` pe secure aate hain (lock icon ✅)
5. ACM **auto-renew** bhi karega — kabhi expire nahi hoga

**ACM — kya free hai, kya nahi?**

| | ACM (public) | Third-party (DigiCert, GoDaddy, etc.) |
|--|------------|--------------------------------------|
| Price | **Free** | ~$50-$1000/year |
| Renewal | **Auto** (ACM karega) | Manual |
| AWS integration | **Seamless** (ALB, CloudFront, API Gateway) | Manual upload |
| Validation | DNS ya Email | Domain owner verify karna padta hai |

**Certificate kaise request karte hain?**

1. **Request certificate**
   - ACM → "Request certificate" → "Public certificate"
   - Domain name daalo: `shop.example.com`

2. **Validation karo** (proof ki domain aapki hai)
   - **DNS validation (recommended):** Route 53 me ek CNAME record automatic add kar do
   - **Email validation:** Certificate wali team domain owner ke email pe verification bhejegi

3. **Certificate issue ho jata hai** (generally 5-10 min)

4. **Use karo** — certificate ko attached karo:
   - **ALB** → Listeners → HTTPS:443 → Certificate select karo
   - **CloudFront** → Custom SSL Certificate → Select karo
   - **API Gateway** → Custom Domain → Certificate select karo

**Important Points:**
- ACM certificates **sirf us region me** use ho sakte hain jahan request kiye ho
- **Exception:** CloudFront ke liye certificate **us-east-1 (N. Virginia)** me hona chahiye
- Public certificates **free** hain — private (internal) certificates ke liye **Private CA** chahiye
- Certificate **auto-renew** hota hai — jab tak DNS record hataya nahi gaya
- **ACM se private key kabhi download nahi kar sakte** — sirf AWS services pe use kar sakte ho
- **Multiple domains** — ek certificate me `*.example.com` (wildcard) + `example.com` dono add kar sakte ho

## Cognito

AWS Cognito ek **authentication (login/signup) + authorization (permissions) service** hai. Ye aapki app ke liye user management handle karti hai — aapko khud login system banana nahi padta.

**Example — Amazon ka login:**
Jab aap amazon.com pe signup karte hain:
1. **Sign up** — apna email/password daalte ho
2. **Email verification** — Amazon aapko OTP bhejta hai
3. **Login** — baar baar email/password se login karte ho
4. **Forgot password** — "forgot password?" click karte ho, reset link aata hai
5. **Third-party login** — "Sign in with Google/Facebook" bhi kar sakte ho

Ye sab Cognito out-of-the-box provide karta hai. Khud banana padta to months lagte.

**Cognito — 2 main parts:**

| Part | Kya karta hai |
|------|-------------|
| **User Pool** | User directory + signup/login UI + email verification + forgot password |
| **Identity Pool** | Temporary AWS credentials dena (users ko S3, DynamoDB access dene ke liye) |

### User Pool

User Pool ek **user database** hai jisme users store hote hain.

**Key features:**
- **Sign up / Sign in** — koi bhi user register/login kar sakta hai
- **Email/Phone verification** — OTP ya link se verify
- **MFA** — OTP ya authenticator app (Google Authenticator)
- **Forgot password** — auto reset flow
- **Custom fields** — apne hisaab se fields add karo (jaise `favourite_genre`)
- **Hosted UI** — Cognito khud ka login page deta hai (custom domain bhi laga sakte ho)
- **Social login** — Google, Facebook, Apple, Amazon se login
- **SAML/OpenID** — corporate login (Azure AD, Okta, etc.)

**Setup steps — User Pool:**

1. **User Pool banao**
   - Cognito → "Create user pool"
   - Sign-in options: Email / Phone / Username (or sab)
   - Password policy set karo

2. **Verification configure karo**
   - Email verification: Auto (Cognito SES use karta hai) ya Custom (apna email provider)
   - Phone verification: SMS (Amazon SNS se bhejta hai)

3. **Hosted UI enable karo**
   - "Hosted authentication pages" → ON
   - App client banao (web ya mobile app)
   - Callback URL daalo (jaise `https://myapp.com/callback`)

4. **App client set karo**
   - **Confidential client** — server-side app (Node.js, Django) — client secret hota hai
   - **Public client** — frontend app (React, Android, iOS) — client secret nahi hota

5. **App me SDK integrate karo**
   - **Amplify** (React/React Native/Flutter)
   - **Cognito SDK** (Node.js, Python, Java, Go)
   - **AWSCognitoIdentityProvider** API

**Hosted UI example:**
```
User → "Login" click → Cognito Hosted UI pe redirect → Login karo → Callback URL pe aao
```
Aapko sirf redirect handle karna hai — login page, forgot password, MFA sab Cognito sambhalta hai.

### Identity Pool

Identity Pool AWS resources ka access dene ke liye hai — users ko **temporary AWS credentials** (access key + secret key + session token) deta hai.

**Example — profile photo upload:**
1. User app me login karta hai (User Pool)
2. App **Identity Pool** se temporary credentials leta hai
3. Ab user directly **S3 bucket** me profile photo upload kar sakta hai
4. Aapne backend ki zaroorat nahi padi — frontend se directly S3 me file gaya

**Flow:**
```
User Login → Cognito User Pool (authenticate)
         ↓
Cognito Identity Pool (temporary AWS credentials)
         ↓
User ab S3/DynamoDB/API Gateway access kar sakta hai
```

**Setup steps — Identity Pool:**

1. **Identity Pool banao**
   - Cognito → "Identity Pools" → "Create"
   - Name do

2. **Auth provider connect karo**
   - **Cognito User Pool** — aapka User Pool select karo
   - **Public providers** — Google, Facebook, Apple
   - **OpenID / SAML** — koi bhi identity provider

3. **IAM roles set karo**
   - **Authenticated role** — login users ke liye permissions (jaise S3 read)
   - **Unauthenticated role** — guest users ke liye limited access
   - Ye roles baad me IAM me edit kar sakte ho

4. **App me use karo**
   - Amplify ya AWS SDK se Identity Pool se credentials fetch karo
   - S3 upload, DynamoDB query — sab frontend se directly

**User Pool vs Identity Pool — simple difference:**
| | User Pool | Identity Pool |
|---|-----------|---------------|
| Kaam | **Login karo** (who are you?) | **Access do** (what can you do?) |
| Store kya karta hai | Username, password, email | Kuch nahi (temporary credentials deta hai) |
| Output | JWT token (ID + Access + Refresh) | Temporary AWS creds (Access Key + Secret + Session) |
| Analogy | **ID card** — pehchaan patrika | **Entry pass** — andar kahan kahan ja sakte ho |

**Cognito triggers (Lambda customisation):**
Cognito Lambda functions ko trigger kar sakta hai — apni custom logic laga sakte ho:

| Trigger | Kab chalega |
|---------|------------|
| Pre Sign-up | Signup se pehle — manual approval, email domain check |
| Post Confirmation | User verified ho jane ke baad — user ko DynamoDB me save karo |
| Pre Authentication | Login se pehle — IP block karo, account check |
| Post Authentication | Login ke baad — login history log karo |
| Custom Message | Email/SMS template customize karo |
| Define Auth Challenge | Custom auth challenge (CAPTCHA, pattern lock, etc.) |
| Create Auth Challenge | Challenge generate karo |
| Verify Auth Challenge | User ka response verify karo |

**Analogy — poora flow:**
```
[User] → Sign Up → User Pool (user save ho gaya)
   → Email verify → OTP check
   → Login → User Pool JWT token deta hai
   → Profile photo upload → Identity Pool se S3 credentials liye
   → S3 me photo upload kiya (bina backend ke!)
```

## EC2 (Elastic Compute Cloud)

EC2 ek **virtual server** hai jo cloud me chalta hai. Aap apna computer ki tarah ise use kar sakte ho — app run karo, website host karo, background processing karo.

**Example — Amazon ka product listing server:**
Amazon ke har product page ke peeche ek EC2 instance hai jo:
1. Database se product details fetch karta hai
2. HTML page generate karta hai
3. User ko response bhejta hai

**EC2 key points:**
- **OS** — Windows, Linux (Amazon Linux, Ubuntu, RedHat) choose karo
- **Instance types**:
  - **t2.micro / t3.micro** — small, free tier (testing)
  - **m5.large** — general purpose (web servers)
  - **c5.2xlarge** — compute optimized (CPU heavy)
  - **r5.large** — memory optimized (large databases)
- **Pricing models:**
  - **On-Demand** — per second pay (flexible, expensive for 24x7)
  - **Reserved** — 1/3 year commit (cheaper, ~40% save)
  - **Spot** — unused capacity at 90% discount (can be terminated anytime)

**How it works:**
```
EC2 Instance (t2.micro, Ubuntu, 8GB storage)
   ├── Web server (Apache/Nginx) → HTTP requests handle
   ├── App server (Node.js/Python/Java) → business logic
   └── Database (MySQL/PostgreSQL) → data store
```

**Analogy:** Apna ek **personal computer** hai jo 24x7 chalta hai, kisi bhi size ka le sakte ho, aur jab chaho band kar do.

## Lightsail

Lightsail AWS ka **simplified, fixed-price VPS** hai. Ye un logo ke liye hai jinhe EC2 ki complexity nahi chahiye — bas ek simple server chahiye fixed monthly price me.

**Example — chhoti blog/website:**
Aap ek personal blog host karna chahte ho:
- **EC2 se:** VPC, security groups, IAM roles, subnets — sab configure karna padega
- **Lightsail se:** `$5/month` ka plan lo → WordPress install karo → blog ready in 5 min

**Lightsail vs EC2:**

| Feature | Lightsail | EC2 |
|---------|-----------|-----|
| Setup | Simple — 5 min | Complex — networking, security groups |
| Pricing | **Fixed monthly** ($3.50 to $160) | Per second, variable |
| VPC | Built-in (auto) | Manual |
| Scaling | Manual (bigger plan le lo) | Auto-scaling groups |
| Best for | Small apps, blogs, dev projects | Production, large scale, complex apps |
| Analogy | **Hostel room** — sab included, limited size | **Apna ghar** — full freedom, sab khud manage |

**Key features:**
- Fixed pricing — predictable bill
- Pre-configured apps: WordPress, Node.js, LAMP, MEAN, Magento
- Built-in monitoring, backups, DNS management
- Can be upgraded to EC2 later if needed

**When to use Lightsail vs EC2:**
- **Lightsail:** Pet project, learning, small business website, $5-$20/month budget
- **EC2:** Production app, auto-scaling, complex networking, compliance requirements

## ECS (Elastic Container Service)

ECS ek **container orchestration** service hai jo Docker containers ko run karti hai. Aapko servers manage nahi karne padte — bas container defin karo, ECS chalayega.

**Example — Amazon ka microservices architecture:**
Amazon ki app alag-alag chhoti services se bani hai:
- Search service (ek container)
- Cart service (doosra container)
- Payment service (teesra container)
- Recommendation service (chauta container)
ECS in sab containers ko manage karta hai — kaunsa container kahan chalega, kisko restart karna hai, kaise scale karna hai.

**Key concepts:**
- **Task Definition** — container ka blueprint (image, port, CPU, memory)
- **Task** — ek running container (ya container group)
- **Service** — ensures X containers always running (auto-heal + load balancer integration)
- **Cluster** — ECS ka logical group jahan tasks chalte hain
- **Container image** — Docker image stored in **ECR (Elastic Container Registry)**

**What does orchestration mean?**
```
Aap define karte ho: "Mujhe 5 containers chahiye, har ek 512MB RAM, port 3000"
ECS automatically:
   1. Containers start karta hai
   2. Monitor karta hai — healthy ya nahi
   3. Koi fail hua to naya start karta hai
   4. Load increase hui to scale up karta hai
   5. Update aaya to rolling deploy karta hai
```

**Analogy:** ECS = **Property manager** — aap building owner ho, manager sab tenants (containers) ka dhyan rakhta hai.

## Fargate

Fargate ECS ka **serverless** version hai. ECS me aap servers manage karte ho (EC2 instances), Fargate me aap sirf container dete ho — baaki sab AWS sambhalta hai.

**Example — API backend:**
Aapne Node.js ka API banaya hai Docker me:
- **ECS (EC2):** Pehle EC2 instance launch karo, uske baad container uspe chalao — server ka dhyan rakhna padega
- **Fargate:** Simply container define karo → Fargate chala dega → koi server nahi dikhta

**ECS (EC2 launch type) vs Fargate:**

| Feature | ECS (EC2) | ECS (Fargate) |
|---------|-----------|---------------|
| You manage | EC2 instances, scaling, patches | **Nothing** — sirf container |
| AWS manages | Nothing | Servers, OS, patches |
| Pricing | EC2 cost (24x7) | Per **task per second** |
| Best for | Large workloads, predictable cost | Variable traffic, simplicity |
| Cold start | ❌ (server already running) | ✅ (container start hota hai) |

**Fargate pricing example:**
- 1 container, 512MB RAM, 0.25 vCPU, 1 hour run
- Cost: ~$0.012/hour (server ka bill nahi, sirf container ka)

**Analogy:**
- **ECS (EC2):** Khud ka **flat** liya hai — furniture khud lao, maintenance khud karo
- **Fargate:** **Hotel room** book kiya — sirf aap ka samaan lao (container), baaki sab hotel sambhalta hai

## EKS (Elastic Kubernetes Service)

EKS ek **managed Kubernetes** service hai. Kubernetes containers orchestrate karne ka industry-standard tool hai, aur EKS usko AWS pe manage karta hai.

**Example — Netflix ka multi-service architecture:**
Netflix 500+ microservices chalat hai — recommendation, streaming, billing, user profile, etc.
- EKS Kubernetes cluster chalata hai
- Kubernetes decide karta hai kaunsa container kaha chalega
- Auto-scaling, self-healing, rolling updates — sab Kubernetes karta hai

**ECS vs EKS — kab kya use karein?**

| Feature | ECS | EKS |
|---------|-----|-----|
| AWS specific | ✅ (only AWS) | ❌ (portable — GKE, AKS bhi chal sakta hai) |
| Complexity | **Simple**, AWS integrated | **Complex** — steep learning curve |
| Community | AWS specific | Huge — open source, multi-cloud |
| Features | Basic but enough | Advanced (service mesh, operators, CRDs) |
| When to use | Dono AWS ho, simplicity chahiye | Multi-cloud, complex deployments, K8s expertise ho |

**Key Kubernetes concepts:**
- **Pod** — smallest unit (1+ containers)
- **Deployment** — desired state defines (5 replicas, image: v2)
- **Service** — stable endpoint (load balancer pods ke aage)
- **ConfigMap / Secret** — config injection
- **Ingress** — external access (like API Gateway)

**Analogy:** Kubernetes = **Ship captain** — jahaz (servers) aur container dock workers ko manage karta hai. EKS = **Captain hiring service** — AWS captain hire kar ke apne jahaz pe bhejta hai.

## Lambda

Lambda ek **serverless compute** service hai jo code tab chalati hai jab koi event trigger kare. Aapko server nahi khareedna, nahi manage karna — bas code upload karo, Lambda automatically chalayega.

**Example — Amazon ki order confirmation email:**
Jab aap Amazon pe kuch order karte hain:
1. Order system se event aata hai
2. **Lambda function trigger** hota hai
3. Lambda email template bana kar **SES** (email service) ko bhejta hai
4. User ko confirmation email chala jata hai
5. Lambda ka kaam khatam — it shuts down
6. Aapne koi server nahi khareeda, koi EC2 nahi chalaya

**Example — Image upload automatic resize:**
User apni photo S3 me upload karta hai:
1. S3 pe file upload hoti hai (trigger)
2. **Lambda function chalta hai**
3. Lambda photo read karta hai, compress karta hai, resize karta hai
4. Resized image wapas S3 me save karta hai
5. Lambda band ho jata hai

**Lambda key features:**
- **Event-driven** — S3 upload, API Gateway call, DynamoDB change, scheduled (cron)
- **Auto-scaling** — ek saath 1 ya 1000 Lambda instances chal sakte hain
- **Pay per use** — sirf tab pay karo jab code chale (per 1ms + per request)
- **Free tier** — 1M requests/month free, 400,000 GB-seconds free
- **Limits** — max 15 min timeout, 10GB disk, 10,240MB memory
- **Cold start** — pehli baar Lambda start hone me ~100ms-1sec lagta hai

**Lambda vs EC2 vs ECS/Fargate:**

| Feature | EC2 | ECS/Fargate | Lambda |
|---------|-----|-------------|--------|
| Management | Full control | Container level | **Zero** |
| Duration | 24x7 | 24x7 | **Max 15 min** |
| Scale | Manual / ASG | Manual / auto | **Instant auto** |
| Startup | Always on | Always on | **Cold start** |
| Price | Per hour | Per hour/per task | **Per ms** |
| Use case | Long-running, stateful | Batch, microservices | **Short, event-driven** |
| Analogy | **Khud ka restaurant** | **Cloud kitchen** | **Zomato delivery boy** — jab order aaye tab kaam kare |

**Lambda triggers (kya kya Lambda chala sakta hai):**
- S3 (file upload/delete)
- API Gateway (HTTP request)
- DynamoDB Streams (data change)
- SQS / SNS (message queue)
- CloudWatch Events (scheduled/cron)
- Cognito (signup, login)
- Alexa (skill kit)
- CloudFront (Lambda@Edge)
- Step Functions (workflow step)

**Lambda use cases:**
| Use case | Example |
|----------|---------|
| Image processing | Upload photo → compress + resize |
| API backend | API Gateway + Lambda + DynamoDB |
| File processing | S3 file → transform → save |
| Cron jobs | Daily report generate karo |
| Real-time processing | IoT data → process → store |
| Notification | Order placed → email/SMS bhejo |

## EBS (Elastic Block Store)

EBS ek **block-level storage** volume hai jo EC2 instance ke saath attach hota hai. Jaise aapke laptop ki hard disk — waise hi EBS EC2 ka **local hard drive** hai.

**Example — Amazon ka product listing server:**
Amazon ka EC2 instance product details serve karta hai:
1. EC2 instance chalta hai with **EBS volume** attached (100GB SSD)
2. Is EBS pe OS (Ubuntu) installed hai
3. Web server (Apache) aur code bhi yahi hai
4. Product images bhi EBS pe store hote hain
5. EC2 band kiya to data EBS me bacha rahta hai
6. Dobara start kiya to wahi data available hai

**EBS key features:**
- **Persistent** — EC2 band/delete karo to bhi data safe
- **Snapshots** — backup le sakte ho (S3 me stored, incremental)
- **Encryption** — AES-256 encryption (free)
- **Resize** — volume size increase kar sakte ho (downgrade nahi)
- **Types:**
  | Type | Use case | Max IOPS |
  |------|----------|----------|
  | gp3 (SSD) | General purpose, web servers | 16,000 |
  | io2 (SSD) | High performance, databases | 256,000 |
  | st1 (HDD) | Frequently accessed, big data | 500 |
  | sc1 (HDD) | Cold data, backups | 250 |
- **Max size** — 16TB per volume

**EBS vs EC2 Instance Store:**
| Feature | EBS | Instance Store |
|---------|-----|---------------|
| Data persists | ✅ EC2 stop/start ke baad bhi | ❌ EC2 stop hua to data gayab |
| Backup | Snapshots | ❌ No backup |
| Detach/Attach | ✅ Doosre EC2 me laga sakte ho | ❌ Fixed |
| Speed | Fast | **Extremely fast** (physical server pe hai) |
| Use case | Persistent storage, databases | Temporary data, cache |

## EFS (Elastic File System)

EFS ek **managed NFS (Network File System)** hai jo multiple EC2 instances ek saath access kar sakte hain. Jaise office ka shared network drive — sab computers ek hi file system access kar sakte hain.

**Example — Amazon ke load balancer ke peeche servers:**
Amazon ke 10 EC2 instances hain (load balancer ke peeche):
1. Sabhi instances **same shared folder** access karte hain — EFS
2. User koi bhi server pe jaye, same files dikhti hain
3. Agar koi EC2 user upload/image add karta hai to sabhi instances immediately access kar sakte hain
4. **No need to sync** files between servers — EFS handle karta hai

**EFS key features:**
- **Shared storage** — 1000s of EC2 ek saath connect ho sakte hain
- **Auto-scaling** — file system automatically expand/shrink hota hai (pay for what you use)
- **Multi-AZ** — multiple availability zones me accessible
- **POSIX compliant** — Linux apps directly mount kar sakte hain (`sudo mount -t nfs4 ...`)
- **Lifecycle management** — unused files automatically cheaper tier me move

**EBS vs EFS — main difference:**

| Feature | EBS | EFS |
|---------|-----|-----|
| Connection | **1 EC2** ek time pe (or multi-attach io2) | **1000s of EC2** ek saath |
| Sharing | ❌ One-to-one | ✅ One-to-many |
| Scope | **Single AZ** (same availability zone) | **Multi-AZ** (regional) |
| Use case | OS disk, database | Shared files, web server content, media |
| Analogy | **Laptop ki hard disk** — sirf apna computer use karega | **Office ka shared drive** — sab computers access kar sakte hain |
| Backup | Snapshots (manual/auto) | EFS-to-EFS backup (AWS Backup) |
| Price | GB-month (provisioned, pay even if empty) | GB-month (only what you use, auto-scale) |

**When to use what?**
| Scenario | Use |
|----------|-----|
| EC2 OS disk | **EBS** (root volume) |
| Database (MySQL, PostgreSQL) | **EBS** (io2 for high performance) |
| Multiple servers same files chahiye | **EFS** |
| WordPress images shared across servers | **EFS** |
| Big data processing (temporary) | **Instance Store** |
| Backup data | **S3** (cheaper than EBS) |

**Real-world architecture example:**
```
Load Balancer
    ↓
EC2 #1  EC2 #2  EC2 #3  (Auto Scaling group)
    \      |      /
     \     |     /    
        EFS (shared storage — images, uploads, configs)
        
Har EC2 ka apna EBS (OS + app code)
Sab EC2 same EFS mount karte hain (shared content)
```

## Secrets Manager

AWS Secrets Manager ek **secure secret storage** service hai jisme aap passwords, API keys, database credentials, aur sensitive data store karte ho. Secrets encrypted hote hain aur **auto-rotate** bhi ho sakte hain.

**Example — Amazon ka database password:**
Amazon ka app MySQL database se connect hota hai:
1. **Pehle (wrong):** Developer `config.js` me hardcode karta hai — `password = "admin123"`
   - Code leak hua → password leak
   - Password change kiya to har server me manual update karna padega
2. **Secrets Manager se:**
   - Database password Secrets Manager me store hai
   - App code me sirf ek API call: `getSecret("prod/db/password")`
   - Koi hardcode nahi, koi leak nahi
   - Password auto-rotate hota hai (30 din me ek baar)
   - Sab servers automatically naya password le lete hain

**Secrets Manager vs Parameter Store (SSM):**

| Feature | Secrets Manager | Parameter Store (Free) |
|---------|----------------|----------------------|
| Price | **$0.40/secret/month** + $0.05/10k API calls | **Free** (standard), $0.05/parameter (advanced) |
| Auto-rotation | ✅ (Lambda trigger) | ❌ |
| Encryption | ✅ Always (KMS) | ✅ (optional, with KMS) |
| Cross-account access | ✅ (resource policy) | ❌ (limited) |
| Max size | 64KB | 8KB (standard), 8KB (advanced) |
| When to use | Database passwords, API keys needing rotation | Config values, non-sensitive data, free option |

**Key features:**
- **Encryption at rest** — AWS KMS se automatically encrypt
- **Auto-rotation** — Lambda function banao jo password rotate kare (RDS, Redshift, DocumentDB)
- **Fine-grained access** — IAM policies se control karo kaun kaunsa secret access kar sakta hai
- **Secret versioning** — purane versions bhi available hain (rollback possible)
- **Cross-region replication** — secrets doosre region me bhi replicate kar sakte ho

**How it works:**
```
Aapka App (EC2/Lambda)
    ↓ (API Call: GetSecretValue)
Secrets Manager
    ↓ (Decrypt with KMS)
Plaintext password/API key return
```

**Common use cases:**
| Secret | Store karo | Rotation |
|--------|-----------|----------|
| RDS/MySQL password | ✅ Secrets Manager | ✅ Auto (built-in RDS rotation) |
| API key (Google, Stripe, etc.) | ✅ Secrets Manager | ✅ Custom Lambda |
| GitHub token | ✅ Secrets Manager | ✅ Custom Lambda |
| SSH key | ✅ Secrets Manager | ❌ (manual) |
| App config (not sensitive) | **Parameter Store** (free) | ❌ |

**Setup steps:**

1. **Secret banao**
   - Secrets Manager → "Store a new secret"
   - Secret type: RDS database / Other (API key, custom)
   - Key-value pairs daalo (username, password, host, port)

2. **Encryption key select karo**
   - Default KMS key (aws/secretsmanager) ya apna custom KMS key

3. **Auto-rotation enable karo** (recommended)
   - Lambda function select karo ya create karo
   - Rotation schedule (30 days, 60 days, etc.)

4. **App me use karo**
   ```python
   # Example: Python mein secret lena
   response = client.get_secret_value(SecretId='prod/db/password')
   secret = json.loads(response['SecretString'])
   db_password = secret['password']
   ```

5. **IAM permissions do**
   - EC2/Lambda ke IAM role me `secretsmanager:GetSecretValue` permission do
   - Sirf wahi applications access karein jinhe zaroorat hai

**Important Points:**
- Secrets Manager **$0.40 per secret per month** + API call charges — dhyan rakho cost ka
- Agar rotation ki zaroorat nahi hai, to **SSM Parameter Store (free)** use karo
- Secret access **CloudTrail me log** hota hai — pata chalega kisne kab access kiya
- **Cache karo** secret ko app me (1 hour TTL) — baar baar API call na karo (cost + rate limit)
- Rotation ke waqt **old secret bhi valid rehta hai** (grace period) — downtime nahi aati

## AppConfig

AWS AppConfig ek **app configuration management** service hai jo aapko **bina deploy kiye** app ke settings/features change karne deti hai. Jaise feature flag, toggle, aur config values ko real-time update kar sakte ho — code change nahi, deploy nahi, app restart nahi.

**Example — Amazon ka "Dark Mode" feature:**
Amazon ne dark mode feature banaya hai but abhi sab users ko nahi dena chahte:
1. **Pehle:** Dark mode code ready hai, but conditional check se sab ke liye OFF hai
2. **AppConfig me:** `dark_mode_enabled` ek **flag** hai — currently `false`
3. **Ab:** 10% users ke liye enable karna hai → AppConfig me value `true` karo
4. **Result:** App restart nahi karna pada, deploy nahi karna pada — bas flag toggle kiya
5. **Rollback:** Issue aaya to wapas `false` karo — turant

**AppConfig vs Environment Variables vs Parameter Store:**

| Feature | Env Variables (hardcoded) | Parameter Store | AppConfig |
|---------|--------------------------|----------------|-----------|
| Change method | Deploy | API call | **Gradual rollout** |
| Change without deploy | ❌ | ✅ | ✅ |
| Validation before apply | ❌ | ❌ | ✅ |
| Gradual rollout | ❌ | ❌ | ✅ (10%, 50%, 100%) |
| Auto-rollback | ❌ | ❌ | ✅ (monitor + revert) |
| Jeda lagaya | ❌ | ❌ | ❌ (instant on all serves) |

**Key features:**
- **Feature flags** — code me boolean toggle
- **Config values** — API endpoint URL, timeouts, throttle limits
- **Validator** — config deploy se pehle validate karo (JSON schema, Lambda)
- **Deployment strategy** — Linear (equal %), Canary (10% → 50% → 100%), All-at-once
- **Auto-rollback** — monitoring alarm trigger hua to automatic revert
- **Hosted config** — AppConfig me hi store karo ya external (S3, SSM, Parameter Store)

**How it works:**
```
Developer → AppConfig me config update karta hai
         ↓
AppConfig validates config (schema check, Lambda)
         ↓
Gradual rollout start hota hai (e.g., 10% servers)
         ↓
App monitors — error rate badhi? → Auto-rollback
         ↓
Everything healthy → 50% → 100% rollout complete
```

**AppConfig components:**
- **Application** — aapki app ka logical name (jaise "ShoppingApp")
- **Configuration Profile** — config ka type (feature flag, timeout settings)
- **Strategy** — kaise deploy karna hai (Canary, Linear, All-at-once)
- **Environment** — dev, test, prod (alag-alag config)
- **Configuration data** — actual config values (JSON/YAML/text)
- **Validator** — Lambda ya JSON schema jo config check kare

**Comparison — kab kya use karein:**

| Scenario | Use |
|----------|-----|
| Database password, API key | **Secrets Manager** |
| App timeout, max retries, URLs | **AppConfig** |
| Dark mode, new UI toggle | **AppConfig** (feature flag) |
| A/B test — 10% users ko new feature | **AppConfig** |
| Static non-sensitive config | **SSM Parameter Store** (free) |
| Deploy-time config (Docker env) | **Environment variables** |

**AppConfig vs LaunchDarkly:**
- **LaunchDarkly** — third-party, better UI, but expensive
- **AppConfig** — AWS native, cheaper, AWS ecosystem me integrated

**Simple example — code me feature flag:**
```python
# Without AppConfig — hardcoded
if user.is_eligible:
    show_dark_mode = True

# With AppConfig — dynamic
config = appconfig_client.get_configuration(
    application="ShoppingApp",
    configuration="feature_flags",
    client_id=server_id
)
if config["dark_mode_enabled"] and user.is_eligible:
    show_dark_mode = True
```

**Important:**
- AppConfig **deployment nahi** hai — ye runtime config change hai
- Secret values ke liye AppConfig mat use karo — **Secrets Manager** use karo
- AppConfig + **CloudWatch alarms** lagao — auto-rollback automatic hoga
- Har config change **CloudTrail me log** hota hai — audit trail milta hai

## Database Services — Overview

```
AWS Databases
    ├── Relational (SQL) → RDS, Aurora
    ├── NoSQL (Key-Value) → DynamoDB, Keyspaces
    ├── Document → DocumentDB (MongoDB)
    ├── Graph → Neptune
    └── Search → OpenSearch
```

## RDS (Relational Database Service)

RDS ek **managed relational database** service hai. Aapko DB install, patch, backup kuch nahi karna padta — AWS sambhalta hai. Sirf database create karo aur use karo.

**Example — Amazon ka product database:**
Amazon ke saare products (name, price, category, stock) ek **relational database** me store hote hain:
```sql
SELECT * FROM products WHERE category = 'Electronics' AND price < 50000;
```
RDS is database ko host karta hai — backup (auto), patching, replication, failover sab automatic.

**Supported database engines:**
| Engine | Best for |
|--------|----------|
| **MySQL** | Open-source, web apps (WordPress, etc.) |
| **PostgreSQL** | Advanced features, analytics, geospatial |
| **MariaDB** | MySQL drop-in replacement |
| **SQL Server** | .NET apps, enterprise |
| **Oracle** | Legacy enterprise apps |
| **Db2** | IBM ecosystem |

**Key features:**
- **Multi-AZ** — ek region me 2 availability zones me replica
  - Primary AZ fail → automatically secondary failover (30-60 sec)
  - **Same region** — sirf disaster recovery, not for scaling reads
- **Read Replicas** — read-only copies for scaling reads (5 per DB)
  - **Cross-region** — doosre region me bhi replica bana sakte ho
- **Automated backups** — daily snapshot (7-35 days retention)
- **Manual snapshots** — aap khud bhi backup le sakte ho (retained forever)
- **Auto patching** — AWS automatically patches DB engine
- **Storage auto-scaling** — disk full hone wali ho to automatically increase
- **Encryption** — KMS encryption at rest, SSL in transit

**RDS Multi-AZ vs Read Replica:**
| Feature | Multi-AZ | Read Replica |
|---------|----------|-------------|
| Purpose | **High availability** (failover) | **Scale reads** (performance) |
| Writes | Allowed (primary) | ❌ (read-only) |
| Failover | ✅ Auto (same region) | ❌ |
| Cross-region | ❌ | ✅ |
| Number | 1 standby | Up to 5 |

**Analogy:** RDS = **Managed hostel mess** — khana banwana hai to chef nahi rakhna, bas order karo aur khao.

## Aurora

Aurora Amazon ka **custom-built relational database** hai — MySQL aur PostgreSQL ke saath compatible hai lekin **5x faster** aur **more reliable**.

**Example — Amazon ka orders system: Amazon ki order system millions orders/sec handle karta hai. Normal MySQL nahi kar sakta. Amazon ne khud ka DB banaya (Aurora) jo MySQL compatible hai lekin 10x performance deta hai.**

**Aurora vs RDS (MySQL/PostgreSQL):**
| Feature | RDS MySQL | Aurora MySQL |
|---------|-----------|-------------|
| Performance | Normal | **5x faster** |
| Storage | Max 16TB (EBS) | **128TB** auto-expand |
| Failover | 30-60 sec | **10-20 sec** (instant) |
| Read replicas | 5 | **15** |
| Replication lag | Higher | **<1 sec** (low latency) |
| Price | Standard | ~20% more expensive |

**Key features:**
- **Auto-scaling storage** — 10GB to 128TB, bina downtime ke
- **Instant failover** — 10-20 sec (Multi-AZ)
- **Global Database** — cross-region replication with <1 sec lag
- **Backtrack** — time travel karo (point-in-time recovery without new DB)
- **Serverless (Aurora v2)** — auto-scale compute, pay per second
- **Clone** — production DB ka clone seconds me (dev/testing ke liye)

**Aurora DB Cluster:**
```
              Writer Endpoint (primary)
              ┌──────────┐
              │ Aurora   │ (Read/Write)
              └──────────┘
                    ↓
         ┌──────────┴──────────┐
    ┌────┴────┐         ┌─────┴─────┐
    │ Reader 1│         │ Reader 2  │ (Read only)
    └─────────┘         └───────────┘
         ↑                    ↑
    Reader Endpoint (auto load balanced)
```

**Analogy:** RDS = **Sardar ji ka dhaba** (normal), Aurora = **5-star restaurant** (fast, expensive, but premium).

## DynamoDB

DynamoDB Amazon ka **NoSQL key-value + document database** hai. Ye **serverless** hai — koi server nahi, koi maintenance nahi, unlimited scale. Milliseconds me response deta hai.

**Example — Amazon ka shopping cart:**
Amazon ke 100 million users hain, har user ka ek cart hai:
```json
{
  "userId": "user_12345",
  "items": ["iPhone 15", "AirPods"],
  "total": 120000,
  "savedAt": "2026-07-20T10:30:00Z"
}
```
DynamoDB is cart ko store karta hai — **millions requests/sec** handle karta hai, koi downtime nahi.

**RDS vs DynamoDB — kab kya?**
| Feature | RDS (SQL) | DynamoDB (NoSQL) |
|---------|----------|-----------------|
| Data model | Tables, rows, columns | **Key-value, documents** |
| Schema | **Fixed** (columns predefined) | **Flexible** (each item different attributes) |
| Queries | SQL (JOIN, GROUP BY, complex) | Simple (key lookup, query, scan) |
| Scale | Vertical (bigger instance) | **Horizontal** (auto, unlimited) |
| Performance | Depends on instance size | **Consistent millisecond** at any scale |
| ACID transactions | ✅ Full | ✅ (limited — transact get/put) |
| Relationships | ✅ (foreign keys, JOINs) | ❌ (denormalized data) |
| Use case | Complex queries, relationships, reporting | High scale, low latency, simple lookups |

**Key concepts:**
- **Table** — data store hota hai yahan
- **Item** — ek record (jaise ek user, ek product) — max 400KB
- **Partition Key** — primary lookup (jaise `userId`) — decides which partition item goes to
- **Sort Key** — optional, items ko order me rakhne ke liye (jaise `timestamp`)
- **GSI (Global Secondary Index)** — doosre partition key se query karna
- **DAX** — DynamoDB Accelerator (in-memory cache, microsecond response)

**Read/Write capacity modes:**
| Mode | Price | Use case |
|------|-------|----------|
| **On-Demand** | Pay per request | Unpredictable traffic, auto-scale |
| **Provisioned** | Pay per RCU/WCU | Predictable traffic, cheaper |

**RCU/WCU:**
- **1 RCU** = 1 strongly consistent read/sec of 4KB item
- **1 WCU** = 1 write/sec of 1KB item

**DynamoDB Streams:**
- Table me koi change (create/update/delete) hota hai to event generate hota hai
- Lambda trigger kar sakte ho — "user profile update hua → email bhejo"

**Analogy:** DynamoDB = **Godown ka almirah** — har almirah me ek label (partition key), andar samaan. Bahut saare almirah, bahut fast, kabhi Bhar nahi.

## DocumentDB

DocumentDB Amazon ka **MongoDB-compatible** managed database hai. MongoDB ki tarah documents (JSON) store karta hai.

**Example — Amazon ka product catalog:**
Har product ki alag properties hoti hain — phone me "screen_size", shirt me "size", book me "pages":
```json
// phone
{ "id": "p1", "name": "iPhone", "type": "phone", "screen_size": 6.1, "price": 79999 }
// shirt
{ "id": "p2", "name": "T-Shirt", "type": "clothing", "size": "L", "price": 999, "color": "blue" }
```
DocumentDB in documents ko store karta hai — SQL databases me har product ke liye alag table banana padta, yahan ek collection me sab adjust ho jata hai.

**DocumentDB vs DynamoDB:**
| Feature | DocumentDB | DynamoDB |
|---------|-----------|----------|
| Data model | **JSON documents** (flexible schema) | Key-value + documents |
| Query language | **MongoDB syntax** (find, aggregate) | **Key lookup + Filter** |
| Indexes | Full MongoDB indexes | Limited (primary key, GSI) |
| ACID transactions | ✅ | ✅ (limited) |
| Use case | MongoDB apps migrate karni hai | New apps, simple lookups at scale |

**When to use DocumentDB:**
- Existing MongoDB app hai — direct migrate karo, code change nahi
- Flexible document storage chahiye (nested JSON)
- MongoDB compatible tools (Mongoose, Compass) use karna hai

## Keyspaces

Keyspaces Amazon ka **managed Apache Cassandra** database hai. Cassandra ek **wide-column NoSQL** database hai jo **petabytes** data handle kar sakta hai.

**Example — Amazon ke device time-series data:**
Amazon ke Alexa devices har second data bhejte hain:
```cql
INSERT INTO device_metrics (device_id, timestamp, cpu, memory, temp)
VALUES ('alexa_123', '2026-07-20T10:30:00', 45.2, 68.1, 36.5);
```
Keyspaces millions writes/sec handle karta hai — perfect for IoT, time-series data.

**Keyspaces vs DynamoDB:**
| Feature | Keyspaces (Cassandra) | DynamoDB |
|---------|----------------------|----------|
| Data model | **Wide-column** (row with 1000s of columns) | Key-value |
| Query language | **CQL** (Cassandra Query Language) | DynamoDB API (AWS SDK) |
| Best for | Time-series, IoT, large writes | General purpose, simple lookups |
| Partitioning | Partition key + Clustering columns | Partition key + Sort key |
| Cassandra compatible | ✅ (existing Cassandra apps migrate) | ❌ |

## Neptune

Neptune Amazon ka **graph database** hai. Ye data ko **nodes** (entities) aur **edges** (relationships) ke form me store karta hai.

**Example — Amazon ka recommendation engine:**
"Users who bought this also bought":
```
[User A] — BOUGHT → [iPhone] — BOUGHT_BY → [User B]
   ↓                    ↓                      ↓
 BOUGHT              BOUGHT                 BOUGHT
   ↓                    ↓                      ↓
[MacBook] ← — — —  [AirPods] ← — — — [Watch]
```
Neptune query karta hai: "User A ne kya khareeda? Un users ne aur kya khareeda jo iPhone khareeda?" — seconds me answer.

**Neptune vs DynamoDB:**
| Feature | Neptune (Graph) | DynamoDB |
|---------|----------------|----------|
| Data | Nodes + Relationships | Key-value pairs |
| Query example | "Kisne kya khareeda aur usne aur kya khareeda?" | "User_123 ki details do" |
| Best for | Social networks, recommendations, fraud detection | Simple lookups, shopping carts, sessions |
| Relationships | **Native** (edges) | **Manual** (join code me karna padega) |

**Neptune use cases:**
- **Social networks** — friends, followers, likes
- **Recommendations** — "customers also bought"
- **Fraud detection** — suspicious transaction patterns
- **Knowledge graphs** — Google Knowledge Graph jaisa
- **Network analysis** — dependencies, infrastructure mapping

**Analogy:** RDS = **Excel sheet**, DynamoDB = **Almirah**, Neptune = **Family tree** — relationships track karna easy hai.

## OpenSearch

OpenSearch (pahle Elasticsearch) ek **search + analytics** engine hai. Full-text search, log analytics, aur real-time visualization ke liye use hota hai.

**Example — Amazon ka search bar:**
Amazon pe "wireless bluetooth headphones noise cancelling" search karo:
1. OpenSearch millions products me **fuzzy search** karta hai
2. Matching products **relevance score** ke hisaab se sort karta hai
3. "Did you mean?" suggestions bhi OpenSearch deta hai
4. Filters (price, brand, rating) — OpenSearch aggregation

**OpenSearch vs RDS (LIKE query):**
| Feature | RDS (LIKE '%keyword%') | OpenSearch |
|---------|----------------------|-----------|
| Search speed | **Slow** (full table scan) | **Milliseconds** (inverted index) |
| Fuzzy search | ❌ (exact match) | ✅ (typo tolerate karta hai) |
| Ranking/scores | ❌ | ✅ (relevance score) |
| Full-text search | ❌ | ✅ |
| Aggregations | ❌ | ✅ (count by category, avg price, etc.) |
| Auto-complete | ❌ | ✅ (prefix/suggestions) |
| Log analytics | ❌ | ✅ (Kibana dashboards) |

**Key components:**
- **Index** — RDS ke table jaisa (data store hota hai)
- **Document** — ek record (JSON format)
- **Shard** — index ka part (horizontally partitioned)
- **Node** — ek server in the cluster
- **Kibana/OpenSearch Dashboards** — visualization tool (graphs, pie charts, logs)

**Common use cases:**
| Use case | Example |
|----------|---------|
| **Search** | E-commerce product search, site search |
| **Log analytics** | Centralized logging (ELK stack) |
| **Application monitoring** | Error tracking, performance metrics |
| **Security analytics** | SIEM (threat detection, log analysis) |
| **Full-text search** | Documents, articles, knowledge base |

**Analogy:** OpenSearch = **Library ka card catalog** — books systematically indexed, koi bhi book turant mil jati hai. RDS ka LIKE = **Purane library me baith ke book search karna**.

## DMS (Database Migration Service)

DMS ek **database migration** service hai jo databases ko **zero downtime** pe migrate karne deti hai — on-premise se AWS, ya ek AWS service se doosri.

**Example — Amazon ka MySQL se Aurora migration:**
Amazon ka purana database MySQL 5.7 pe hai, ab Aurora pe migrate karna hai:
1. **Pehle (manual):** DB band karo → export → import → DB chalu karo → **hours downtime**
2. **DMS se:** Migration chalta rahega, app normal chalti rahegi
   - DMS pehle full data copy karta hai
   - Phir continuously **CDC** (Change Data Capture) — jo bhi change ho raha hai, naye DB pe sync karta hai
   - Ready ho to ek click me switch — **1-2 minute downtime**

**DMS key features:**
- **Zero downtime migration** — app running rehti hai during migration
- **CDC (Change Data Capture)** — real-time changes sync
- **Homogeneous migration** — MySQL → MySQL, Oracle → Oracle
- **Heterogeneous migration** — Oracle → Aurora, SQL Server → PostgreSQL (schema conversion tool - SCT)
- **One-time** ya **ongoing replication**
- **Schema Conversion Tool (SCT)** — automatically schema convert karta hai Oracle/MSSQL se open-source DB me

**Common migration scenarios:**
| From | To | Type |
|------|-----|------|
| On-premise MySQL | RDS MySQL | Homogeneous (easy) |
| On-premise Oracle | Aurora PostgreSQL | Heterogeneous (SCT needed) |
| EC2 MySQL | RDS Aurora | Homogeneous |
| DynamoDB | S3 (export) | Export |
| Any DB | Any DB | Ongoing replication |

**DMS components:**
- **Source endpoint** — existing database connection
- **Target endpoint** — AWS destination (RDS, Aurora, DynamoDB, S3)
- **Replication instance** — EC2 jo migration kar raha hai (size choose karo)
- **Task** — migration task (existing data load + CDC)

**Analogy:** DMS = **Ghar ka shifting van** — purane ghar (on-prem) se naye ghar (AWS) me samaan le jata hai, aur samaan aata rahe to bhi manage kar leta hai (CDC).

## ElastiCache

ElastiCache ek **managed in-memory caching** service hai jo Redis ya Memcached chalati hai. Ye data ko **RAM me store** karta hai — disk se 1000x faster — taki app fast response de sake.

**Example — Amazon ka product page:**
Amazon ka ek product page lakhon users roz kholte hain:
1. **Bina cache:** Har request pe RDS se product details fetch → **50ms** (slow, DB pe load)
2. **ElastiCache se:**
   - Pehli baar: DB se fetch karo → cache me store karo → user ko do
   - Agli baarein: **Cache se directly** → **<1ms** → user ko instant milta hai
   - DB pe load bhi kam hota hai — handles more users

**ElastiCache vs RDS — speed comparison:**

| Storage | Read Speed | Use for |
|---------|-----------|---------|
| **RDS (Disk)** | ~10-50ms | Permanent data, ACID compliance |
| **ElastiCache (RAM)** | **<1ms** | Temporary hot data, session, cache |

**Redis vs Memcached:**

| Feature | Redis | Memcached |
|---------|-------|-----------|
| Data types | String, List, Set, Hash, Sorted Set, Streams | **Only strings** |
| Persistence | ✅ (disk me bhi save kar sakte ho) | ❌ (RAM me hi, restart = data gone) |
| Replication | ✅ (Multi-AZ, read replicas) | ❌ (no replication) |
| Clustering | ✅ (auto sharding) | ✅ |
| Use case | Caching + advanced (queues, sessions, leaderboards) | Simple cache (basic key-value) |
| When to use | Complex data, need persistence/replication | Simple, cheap caching |

**Key features:**
- **Sub-millisecond latency** — RAM me store hai, disk nahi lagti
- **Auto-scaling** — cluster size increase/decrease
- **Multi-AZ** — Redis replication with automatic failover
- **Encryption** — in-transit (TLS) aur at-rest (KMS)
- **Backup/Restore** — Redis snapshots S3 me
- **Managed** — AWS patching, monitoring, failover handle karta hai

**Common use cases:**

| Use case | Example |
|----------|---------|
| **Database cache** | Frequently queried data cache karo (product details, user profiles) |
| **Session store** | User sessions store (shopping cart, login state) |
| **API rate limiting** | Count requests per user, block if limit exceeds |
| **Leaderboard** | Redis Sorted Set — game scores, rankings |
| **Message queue** | Redis Pub/Sub or List — simple queue between services |
| **Real-time analytics** | Count page views, active users |

**Cache patterns — Lazy Loading vs Write-Through:**

| Pattern | Kya hota hai | Pros | Cons |
|---------|-------------|------|------|
| **Lazy Loading** | Miss → DB se lo → cache me daalo | Simple, only caches what's needed | First request slow (cache miss) |
| **Write-Through** | Write ho to cache bhi update karo | Cache always fresh | Writes slower, caches unused data |

**Analogy:** ElastiCache = **Chai ki tapri ke paas rakhne wala chullah** — gas pe chai garam karna (disk) 10 min lagta hai, chullah (RAM) pe 30 sec. Same kaam, but faster.

## MemoryDB

MemoryDB ek **Redis-compatible, durable in-memory database** hai. ElastiCache jaise RAM fast hai, lekin data **kabhi nahi** khota — disk pe bhi store hota hai.

**Example — Amazon ka real-time gaming leaderboard:**
Amazon Games ka live leaderboard:
- Har second 10000 users ke scores update ho rahe hain
- **ElastiCache se:** Fast hai but server crash hua to leaderboard data **lost**
- **MemoryDB se:** Fast bhi hai (RAM me), aur **durable** bhi (disk me multi-AZ write ahead log)
- Server crash hua → restart → data safe

**ElastiCache vs MemoryDB — main difference:**

| Feature | ElastiCache (Redis) | MemoryDB |
|---------|--------------------|----------|
| Purpose | **Cache** (fast, can lose data) | **Database** (fast + durable) |
| Durability | ❌ (restart = data loss without snapshot) | ✅ **Multi-AZ log** — data never lost |
| Speed | <1ms | <1ms (same Redis engine) |
| Persistence | Optional (snapshots) | **Mandatory** (write-ahead log) |
| Multi-AZ | ✅ (failover) | ✅ **Multi-AZ writes** (quorum-based) |
| Use case | Caching, sessions, temporary data | Primary database — real-time apps, gaming, financial |

**When to use what?**

| Scenario | Use |
|----------|-----|
| DB ka cache chahiye (fast, loss ok) | **ElastiCache** |
| Sessions store (logout pe delete, loss ok) | **ElastiCache** |
| Real-time gaming scores (data must not lose) | **MemoryDB** |
| Financial transactions (durable + fast) | **MemoryDB** |
| Simple key-value cache (cheap) | **Memcached** |
| Chat app, message queue | **ElastiCache (Redis)** |

**Key MemoryDB features:**
- **Redis compatible** — Redis clients directly connect kar sakte hain
- **Microsecond reads** — RAM me hai
- **Multi-AZ durability** — 3 AZs me data replicated (2 out of 3 write acknowledgement)
- **Auto-scaling** — cluster resize without downtime
- **Point-in-time recovery** — kisi bhi time pe restore

**Analogy:**
- **ElastiCache** = **Chalkboard** — fast likho/mitao, lekin pani lag gaya to sab khatam
- **MemoryDB** = **Notebook** — fast likho, aur har page photo bhi khinch lo (durable)

## AI/ML Services — Overview

```
AWS AI/ML
    ├── Foundation Models → Bedrock
    ├── ML Training/Hosting → SageMaker
    ├── Image/Video Analysis → Rekognition
    ├── Text-to-Speech → Polly
    └── Speech-to-Text → Transcribe
```

## Bedrock

Bedrock ek **managed foundation model (FM) service** hai jo aapko Amazon, Anthropic, Meta, Mistral, Stability AI ke LLMs (Large Language Models) ko API se use karne deti hai — bina koi model host kiye, bina GPU manage kiye.

**Example — Amazon ka customer support chatbot:**
Amazon ka AI chatbot jo aapki queries handle karta hai:
1. Aap likhte ho: "Mera order abhi tak nahi aaya"
2. Bedrock **Claude (Anthropic)** model ko API call karta hai
3. Claude response generate karta hai: "Aapka order kal tak aa jayega, tracking ID: #12345"
4. Aapko reply milta hai — koi model training nahi, koi server nahi, direct response

**Bedrock key features:**
- **Foundation models** — Claude (Anthropic), Llama (Meta), Titan (Amazon), Mistral, Stable Diffusion
- **No infrastructure** — bas API call karo, AWS model host karta hai
- **Fine-tuning** — apne data se model customize karo (optional)
- **RAG (Retrieval Augmented Generation)** — apne data (S3, knowledge base) se answers dilaao
- **Agents** — Bedrock agent banao jo APIs call kar sake, tasks execute kar sake
- **Guardrails** — toxic content filter, topic restrictions
- **Security** — data AWS ke andar rehta hai, model training ke liye use nahi hota

**Bedrock vs SageMaker:**

| Feature | Bedrock | SageMaker |
|---------|---------|-----------|
| What you do | **Use pre-built models** via API | **Train + host your own models** |
| ML expertise needed | **Minimal** (just API calls) | **High** (data science, ML engineering) |
| Customization | Fine-tuning, RAG | Train from scratch |
| Cost | Pay per token/usage | Pay for training + hosting compute |
| Best for | Integrate AI fast (chat, search, content) | Build custom ML models |

**Supported models (examples):**
| Provider | Models | Best for |
|----------|--------|----------|
| **Anthropic** | Claude 3.5 Sonnet, Haiku | Chat, coding, analysis |
| **Amazon** | Titan Text, Titan Embeddings | Text generation, search |
| **Meta** | Llama 3.1 | Open-source, general purpose |
| **Mistral** | Mistral Large | Multilingual, reasoning |
| **Stability AI** | Stable Diffusion 3 | Image generation |

**Analogy:** Bedrock = **Google Play Store** — apps already bani hui hain, bas download karo aur use karo. SageMaker = **App development studio** — khud app banao.

## SageMaker

SageMaker ek **full ML platform** hai jo machine learning model build, train, deploy karne ke liye hai — data preparation se lekar production hosting tak sab kuch.

**Example — Amazon ka product recommendation model:**
Amazon ne "Customers also bought" feature ke liye ML model banaya:
1. **Data preparation** — past purchase data clean karo (SageMaker Data Wrangler)
2. **Train model** — GPU instances pe model train karo (SageMaker Training)
3. **Tune parameters** — best model version find karo (SageMaker Hyperparameter Tuning)
4. **Deploy** — model API ke roop me host karo (SageMaker Endpoint)
5. **Monitor** — model performance degrade na ho (SageMaker Model Monitor)

**SageMaker components:**
| Component | Kya karta hai |
|-----------|-------------|
| **Studio** | Web IDE for ML (code, visualize, collaborate) |
| **Data Wrangler** | Data clean/transform (no code) |
| **Training** | GPU/CPU instances pe model train karo |
| **Autopilot** | Auto ML — apne data do, model automatically bana de |
| **Endpoint** | Model deploy karo as API |
| **Model Monitor** | Production me model quality track karo |
| **Feature Store** | Features centralized store karo (reuse) |
| **Pipeline** | End-to-end ML workflow (CI/CD for ML) |

**SageMaker vs Bedrock:**
```
SageMaker = Khud ka pizza banake khayenge (base se toppings tak)
Bedrock = Domino's se pizza order karenge (already ready)
```

**Analogy:** SageMaker = **Full kitchen + recipe book** — Everything needed to cook from scratch.

## Rekognition

Rekognition ek **image and video analysis** service hai jo faces detect karti hai, objects identify karti hai, text read karti hai, aur inappropriate content filter karti hai.

**Example — Amazon ka photo gallery face search:**
Amazon Photos me "Merry" search karo — photos me Merry ke saare photos dikh jaate hain:
1. App Rekognition ko bhejti hai: "is photo me kaun hai?"
2. Rekognition detect karta hai: "Face detected — Merry (98% confidence)"
3. Auto-tag hota hai — searchable ho jata hai
4. Millions photos me se seconds me result

**Key features:**
- **Object detection** — "yeh photo me kya hai?" (car, dog, tree, product)
- **Face detection/analysis** — face location, age range, emotion (happy/sad/angry)
- **Face search** — "yeh face pehle kabhi dekha hai?" (celebrity matching)
- **Text detection** — photos/signs me text read karo (OCR)
- **Content moderation** — explicit/inappropriate images filter karo
- **Celebrity recognition** — photo me kaunsi celebrity hai
- **Label detection** — scene description (beach, mountain, office)

**Common use cases:**
| Use case | Example |
|----------|---------|
| **Content moderation** | User-uploaded photos check karo (nudity, violence) |
| **Face verification** | KYC — aadhar photo se live selfie match |
| **Video analysis** | CCTV footage me suspicious activity detect |
| **Text extraction** | Number plate reading, document scanning |
| **Image search** | E-commerce — "red shoes" search pe photo match karo |

**Analogy:** Rekognition = **Bandar kya dekhte hain** — photo dekhte hi bata deta hai kya hai, kaun hai, kya likha hai.

## Polly

Polly ek **text-to-speech (TTS)** service hai jo likhi hui text ko human-like voice me convert karti hai.

**Example — Amazon Alexa bolti hai:**
Jab aap Alexa se puchte ho "aaj mausam kaisa hai":
1. Alexa text generate karta hai: "Aaj Delhi me mausam saaf rahega, 32 degrees Celsius"
2. Alexa ye text **Polly** ko bhejta hai
3. Polly realistic voice me bolta hai — "आज दिल्ली में मौसम साफ रहेगा..."
4. Aapko Alexa ki awaz sunai deti hai

**Key features:**
- **Voices** — 50+ voices across 30+ languages
- **Indian languages** — Hindi, Tamil, Telugu, Bengali, Marathi, etc.
- **Neural TTS** — realistic, natural sounding (better than standard)
- **SSML** — custom pronunciation, pauses, emphasis
- **Speech Marks** — word timestamps (lip-sync ke liye)
- **Lexicon** — custom word pronunciation (jaise "AWS" = "एडब्ल्यूएस")

**Pricing:**
- **Standard voices** — sangat sasta (~$0.000004/character)
- **Neural voices** — ~$0.000016/character

**Use cases:**
| Use case | Example |
|----------|---------|
| **Voice assistants** | Alexa, custom bots |
| **Accessibility** | Visually impaired users ke liye screen reader |
| **E-learning** | Articles/books ko audio me convert |
| **IVR systems** | Call center automated voice |
| **Video narration** | YouTube videos ke liye auto voiceover |

**Analogy:** Polly = **Audiobook reader** — kitab (text) padh ke suna deta hai.

## Transcribe

Transcribe ek **speech-to-text (STT)** service hai jo audio/video se text extract karti hai. Speech ko real-time ya recorded audio se transcribe karti hai.

**Example — Amazon call center recordings:**
Amazon ka customer support call record karta hai (for quality):
1. Call recording stored in S3
2. **Transcribe** audio file leke text banata hai:
   - "Hello, mera order number 12345 hai. Kal deliver hona tha lekin abhi tak nahi aaya."
3. Text searchable ho jata hai — support team baad me analysis kar sakti hai
4. **Sentiment analysis** bhi — customer angry hai ya happy?

**Key features:**
- **Automatic speech recognition (ASR)** — audio → text
- **Real-time streaming** — live transcription (live captions, meetings)
- **Batch processing** — recorded audio files (S3 input)
- **Speaker diarization** — "Speaker 1: ... Speaker 2: ..." (kaun kya bola)
- **Custom vocabulary** — domain-specific words (jaise "EC2", "IAM", "Lambda")
- **Language identification** — automatically detect language
- **Punctuation** — automatic commas, full stops, questions marks
- **Redaction** — PII data (credit card, aadhar) automatically hide karo

**Transcribe vs Polly:**
| | Transcribe | Polly |
|---|-----------|-------|
| Direction | **Speech → Text** (listen karo → likho) | **Text → Speech** (likho → sunao) |
| Input | Audio/Video file | Text string |
| Output | Text (JSON/SRT/VTT) | Audio (MP3/OGG/PCM) |

**Use cases:**
| Use case | Example |
|----------|---------|
| **Call center analytics** | Customer calls transcribe karo → search, analyze |
| **Subtitles/captions** | YouTube/Twitch ke live captions |
| **Meeting transcription** | Zoom/Teams meetings ka auto notes |
| **Voice commands** | "Alexa, lights on" → Transcribe text me convert karta hai |
| **Medical transcription** | Doctor ke dictation notes transcribe |
| **Content search** | Podcasts/videos me search karo specific topic |

**Analogy:** Transcribe = **Typist** — jo bhi bolo, type kar deta hai.

## SNS (Simple Notification Service)

SNS ek **pub/sub (publish/subscribe) messaging** service hai jo ek message ko **multiple receivers** ko bhejti hai — email, SMS, Lambda, SQS, HTTP endpoints, mobile push notifications.

**Example — Amazon ka "Order Confirmation" system:**
Jab aap Amazon pe order karte hain, multiple cheezein hoti hain ek saath:
1. Order place hua
2. **SNS topic "OrderPlaced"** publish hota hai
3. Jo subscribe hain unko message jata hai:
   - **Email** → aapko: "Order confirmed! Your iPhone will arrive tomorrow"
   - **SMS** → warehouse ko: "Pack order #12345"
   - **Lambda** → trigger: "Update inventory database"
   - **SQS** → queue: "Send email notification to user"
4. Sab ek saath hota hai — fault tolerant, loosely coupled

**SNS key concepts:**
- **Topic** — logical channel (jaise "OrderPlaced", "PaymentFailed")
- **Publisher** — jo message bhejta hai (EC2, Lambda, CloudWatch)
- **Subscriber** — jo message leta hai (Email, SMS, Lambda, SQS, HTTP)
- **Message** — JSON ya plain text (max 256KB)

**SNS vs SQS — basic difference:**
| Feature | SNS | SQS |
|---------|-----|-----|
| Pattern | **Pub/Sub** (1 publisher → N subscribers) | **Queue** (1 producer → 1 consumer) |
| Delivery | **Push** (SNS subscribers ko bhejta hai) | **Pull** (consumer SQS se message leta hai) |
| Persistence | ❌ (message deliver → delete) | ✅ (message queue me rehta hai) |
| Use case | Broadcast, notifications | Decouple services, async processing |

**Supported subscribers (receivers):**
| Subscriber | Use |
|------------|-----|
| **Email** | Send email notification to user/admin |
| **SMS** | Send text message to phone |
| **Lambda** | Trigger Lambda function |
| **SQS** | Queue message for async processing |
| **HTTP/HTTPS** | Call external webhook (taap ki tapri API) |
| **Mobile Push** | Send push notification to app (Android/iOS) |
| **CloudWatch** | Create alarm, send to CloudWatch Logs |

**SNS vs SES (Simple Email Service):**
| Feature | SNS | SES |
|---------|-----|-----|
| Channel | Email, SMS, SQS, Lambda, HTTP | **Only Email** |
| Purpose | **Notification system** (event-driven) | **Bulk email sending** (marketing, transactional) |
| Volume | Moderate | **High volume** (millions emails/day) |
| Analytics | Basic | Open rates, bounce rates, click tracking |
| Use case | Order confirmation, alerts, alarms | Newsletters, promotional emails, receipts |

**Fan-out pattern (SNS + SQS):**
```
Order Service → SNS Topic "OrderPlaced"
                   ↓
        ┌──────────┼──────────┐
        ↓          ↓          ↓
    SQS Queue1  SQS Queue2  Lambda
        ↓          ↓
   Email Service  Analytics
```
- One message → SNS → multiple SQS queues
- Har queue apni speed pe process karega
- Ek service slow ya down hai to doosre pe effect nahi

**Filter policies:**
SNS filter karke specific subscribers ko specific messages bhej sakta hai:
```json
// Order topic pe 2 subscribers
Subscriber 1 — Filter: { "type": ["electronics"] }  → sirf electronics orders
Subscriber 2 — Filter: { "type": ["fashion"] }      → sirf fashion orders
```

**Pricing:**
- **$0.50 per 1 million SNS requests**
- **Free tier:** 1 million requests/month
- SMS: pay per text message (varies by country)

**Analogy:** SNS = **WhatsApp Group** — ek baar message likho, group me sabko ek saath chala jata hai.

## SQS (Simple Queue Service)

SQS ek **fully managed message queue** service hai jo microservices ko **decouple** karti hai. Ek service message queue me daalti hai, doosri service apni speed se process karti hai.

**Example — Amazon ka order processing:**
Jab aap Amazon pe order karte hain:
1. **Order Service** order lega aur SQS me message daalega: `{ "orderId": 12345, "userId": "user_99", "items": ["iPhone"] }`
2. **Payment Service** queue se message lega → payment process karega
3. Payment success hua to **Shipping Service** queue se lega → dispatch karega
4. Har service apni speed se kaam karegi — ek slow hai to doosra wait nahi karega

**Without SQS (tight coupling):**
```
Order → Payment → Shipping → Email
         ↓ Agar payment slow hai to sab block
```

**With SQS (decoupled):**
```
Order → [SQS Queue 1] → Payment
Order → [SQS Queue 2] → Fraud Detection
Payment → [SQS Queue 3] → Shipping
```
Har queue independently process hota hai — **no blocking, no timeout, retry possible.**

**SNS vs SQS — detailed comparison:**

| Feature | SNS | SQS |
|---------|-----|-----|
| Pattern | **Push** (publisher → subscriber) | **Pull** (producer → queue → consumer) |
| Delivery | **Immediate push** to subscribers | Consumer **polls/pulls** messages |
| Persistence | ❌ (not stored, delivered once) | ✅ **Up to 14 days** |
| Retry | ❌ (failed = lost) | ✅ (Visibility Timeout + DLQ) |
| Fan-out | ✅ 1 → N subscribers | ❌ 1 → 1 consumer (per queue) |
| Use case | Notifications, alerts, broadcasts | Async processing, decoupling, buffering |

**SQS key features:**
- **Decoupling** — services ek doosre ke baare me nahi jaanti, sirf queue se baat karti hain
- **Auto-scaling** — queue size bada to consumers auto-scale
- **At-least-once delivery** — message ek baar to pahunchta hi hai (duplicate possible)
- **Ordering** — Standard Queue: no guarantee; FIFO Queue: strict order
- **Dead Letter Queue (DLQ)** — failed messages ke liye separate queue (analysis ke liye)
- **Visibility Timeout** — message visible → consumer picks it → hidden (processing) → delete
- **Batching** — ek saath 10 messages send/receive

**SQS Queue Types:**

| Feature | Standard Queue | FIFO Queue |
|---------|---------------|------------|
| Throughput | **Unlimited** | 3000 msg/sec (with batching) |
| Ordering | **Best-effort** (no guarantee) | **Strict FIFO** (first in, first out) |
| Exactly-once | **At-least-once** (duplicates possible) | **Exactly-once** (deduplication) |
| Name suffix | No suffix | Must end with `.fifo` |
| Use case | High throughput, order doesn't matter | Banking, transactions, strict ordering |

**SQS with Auto Scaling:**
```
CloudWatch Alarm (queue length > 100)
         ↓
Auto Scaling Group (spin new EC2)
         ↓
New EC2 starts polling SQS → processes messages
         ↓
Queue empty → ASG scales down
```

**SQS + Lambda:**
```
SQS Queue → Lambda (event source mapping)
Lambda automatically polls SQS and processes messages
```
- Lambda batch size: up to 10 messages per invocation
- If Lambda fails → message returns to queue → retry → DLQ

**Visibility Timeout — how it works:**
```
1. Message in queue (visible)
2. Consumer polls → message becomes "invisible" (visibility timeout starts)
3. Consumer processes...
4a. Success → consumer deletes message
4b. Fail → visibility timeout ends → message visible again for retry
4c. Max retries exhausted → message moves to DLQ
```

**Pricing:**
- **First 1 million requests/month free**
- Standard: $0.40 per million requests (after free tier)
- FIFO: $0.50 per million requests

**Real-world example — e-commerce architecture:**
```
User places order
    ↓
API Gateway + Lambda (Order Service)
    ↓
[SQS Queue: orders.fifo]
    ↓
Payment Service (EC2) polls queue
    ↓
[SQS Queue: payments]
    ↓
Inventory Service + Shipping Service
```
- Agar Payment Service down hai to bhi orders queue me stack hote rahenge
- Payment Service wapas aate hi processing continue — koi loss nahi

**Analogy:** SQS = **Restaurant ka token system** — aap order karte ho (queue me daal do), waiter jab free hota hai tab order process karta hai. Aapko server ke free hone ka wait nahi karna padta.

## EventBridge

EventBridge ek **serverless event bus** service hai jo AWS services, SaaS apps, aur aapke custom apps ke beech events route karti hai. Pahle iska naam "CloudWatch Events" tha.

**Example — Amazon ka "New Seller Registration" flow:**
Jab koi naya seller Amazon pe register karta hai:
1. **Event** generate hota hai: `{ "source": "seller.registration", "type": "NEW_SELLER", "sellerId": "s_999" }`
2. EventBridge ye event **bus** me leta hai
3. **Rules** check karta hai — "kya karna hai is event ke saath?"
4. Match milta hai → trigger hote hain:
   - **Lambda** → "New seller welcome email bhejo"
   - **Step Functions** → "KYC verification process start karo"
   - **SQS** → "Support team queue me daalo"
   - **SNS** → "Admin team ko notify karo"

**EventBridge vs SNS vs SQS:**

| Feature | EventBridge | SNS | SQS |
|---------|------------|-----|-----|
| Pattern | **Event bus** (rules match events) | **Pub/Sub** (topic → subscribers) | **Queue** (producer → consumer) |
| Filtering | **Advanced** (content-based, schema) | Basic (message attributes) | ❌ (no filter) |
| Schema registry | ✅ (event schema discover karo) | ❌ | ❌ |
| Targets | 20+ AWS services + custom HTTP | Email, SMS, SQS, Lambda, HTTP | Lambda, EC2, ECS, SQS itself |
| SaaS integration | ✅ (Zendesk, Shopify, Datadog) | ❌ (SNS webhooks manual) | ❌ |
| Event replay | ✅ (replay past events) | ❌ | ❌ |

**Key features:**
- **Event bus** — events ka central highway
  - **Default bus** — AWS services ke events (EC2 state change, S3 put, etc.)
  - **Custom bus** — aapke custom events
  - **Partner bus** — SaaS apps se events (Datadog, PagerDuty, Zendesk, Shopify)
- **Rules** — filter events + route to targets
- **Schema Registry** — event structure discover karo, code generation
- **Schema Discovery** — automatically detect event schema
- **Event replay** — purane events replay karo (testing/debugging)
- **Archive** — events store karo (up to 1 year)

**EventBridge vs CloudWatch Events:**
```
EventBridge = CloudWatch Events (old) + New Features:
              • Custom event buses
              • Partner integrations (SaaS)
              • Schema registry
              • Event replay
```

**Common targets (where events can go):**
| Target | Use |
|--------|-----|
| **Lambda** | Run serverless function |
| **SQS** | Queue message for processing |
| **SNS** | Send notification |
| **Step Functions** | Start workflow |
| **ECS / EKS** | Run task |
| **Kinesis** | Stream data |
| **API Gateway** | Call HTTP endpoint |
| **CloudWatch Logs** | Log event |

**EventBridge rules example:**
```json
{
  "event-source": ["aws.ec2"],
  "detail-type": ["EC2 Instance State-change Notification"],
  "detail": { "state": ["stopped", "terminated"] }
}
```
→ Agar koi EC2 stop/terminate ho → trigger Lambda to notify admin

**Real-world architecture:**
```
EC2 (state change) ──┐
S3 (file upload)  ──┤
Lambda (error)    ──┤──→ EventBridge Bus ──→ Rules ──→ Targets
Custom App event  ──┤
SaaS (Shopify)    ──┘
```

**Analogy:** EventBridge = **Post office sorting center** — letters (events) aate hain, unka type/pata dekhte hain, fir sahi jagah bhej dete hain. Naya system (EventBridge) advanced filters + schema registry + replay bhi karta hai.

## Step Functions

Step Functions ek **serverless workflow orchestration** service hai jo multiple AWS services ko ek sequence me chain karti hai. Aap ek **state machine** banate ho jisme har step ek task hota hai — Lambda, SQS, SNS, DynamoDB, etc.

**Example — Amazon ka "Order to Delivery" workflow:**
Amazon par iPhone order karne ke baad ye sab hota hai:
```
Order Placed
    ↓
Step 1: Validate Payment → [Lambda]
    ↓
Step 2: Check Inventory → [Lambda → DynamoDB]
    ↓
Step 3 (Parallel):
    ├── Update Inventory → [DynamoDB]
    └── Charge Customer → [Lambda → Payment Gateway]
    ↓
Step 4: Notify Warehouse → [SQS]
    ↓
Step 5: Send Confirmation Email → [SNS → SES]
    ↓
Complete
```
Step Functions is poori workflow ko **orchestrate** karta hai — ek fail hua to retry/rollback, parallel steps, human approval.

**Why not just use Lambda + SQS manually?**
- **Lambda + SQS:** Code me saari logic likhni padegi — retry, error handling, state management, timeouts
- **Step Functions:** Visual drag-drop, auto retry, built-in error handling, execution history

**Key concepts:**
- **State Machine** — aapki workflow ka blueprint
- **State** — ek step in the workflow
- **Task** — actual work (Lambda, Activity, API call)
- **Choice** — if-else branching
- **Parallel** — parallel branches
- **Map** — array ke har item ke liye same task
- **Wait** — delay karo (10 sec, 1 hour, until date)
- **Pass** — input/output manipulate karo
- **Fail / Succeed** — explicit failure/success

**State Machine types:**

| Type | Max Duration | Use Case |
|------|-------------|----------|
| **Standard** | 1 year | Long-running, auditable, exactly-once |
| **Express** | 5 min | High-throughput, at-least-once (cheaper, faster) |

**Standard vs Express:**

| Feature | Standard | Express |
|---------|----------|---------|
| Duration | Up to 1 year | Up to 5 minutes |
| Execution rate | 2000/sec | 100,000/sec |
| Pricing | Per state transition | Per execution + duration |
| History | ✅ Full (CloudWatch Logs) | ✅ (CloudWatch Logs) |
| When to use | Order processing, human approval | IoT events, real-time streaming, high volume |

**Error handling (built-in):**

| Strategy | Kya hota hai |
|----------|-------------|
| **Retry** | Max attempts, backoff, interval set karo |
| **Catch** | Fail hone par specific path pe bhejo (fallback) |
| **Timeout** | Har state ka max time limit set karo |
| **Heartbeat** | Long tasks — periodic "I'm alive" signal |

**Example — retry policy:**
```json
"Retry": [
  {
    "ErrorEquals": ["Lambda.ServiceException"],
    "IntervalSeconds": 2,
    "MaxAttempts": 3,
    "BackoffRate": 2
  }
]
```
Fail → wait 2 sec → retry → wait 4 sec → retry → wait 8 sec → fail

**Human approval pattern:**
```
Task A → Send Approval Email → Wait for callback
    ↑
[SNS Email: "Approve this order?"]
    ↓
User clicks "Approve" → API Gateway → Step Functions callback → Continue
```

**Step Functions vs EventBridge:**

| Feature | Step Functions | EventBridge |
|---------|---------------|-------------|
| Purpose | **Workflow orchestration** (sequential/parallel steps) | **Event routing** (event → target) |
| State | ✅ **Stateful** — remembers where you are | ❌ Stateless |
| Long-running | ✅ (1 year) | ❌ (event instant) |
| Human approval | ✅ (pause + wait for callback) | ❌ |
| Error handling | ✅ Built-in (retry, catch) | ❌ (rely on target) |
| Visual debugging | ✅ (execution graph) | ❌ |

**When to use what:**
- **EventBridge:** "Event aaya → karo" (fire and forget)
- **Step Functions:** "Karo ye, phir wo, phir ye — agar fail hua to retry" (orchestration)

**Step Functions + Lambda = Serverless workflow:**
```
API Gateway → Step Functions → Lambda → DynamoDB → SNS
```
Transactions rollback, retry logic, timeout handling — sab built-in.

**Pricing:**
- **Standard:** $0.025 per 1000 state transitions
- **Express:** $1.00 per 1 million executions + $0.0001 per 100ms duration

**Analogy:** Step Functions = **Factory assembly line** — product (data) ek step se doosre step pe jaata hai, har station kuch kaam karta hai. Koi station fail ho to robot (retry) fix karta hai, nahi to supervisor (catch) ko inform karta hai.

## MWAA (Amazon Managed Workflows for Apache Airflow)

MWAA AWS ka **managed Apache Airflow** service hai. Apache Airflow ek open-source **workflow orchestration** tool hai jo data pipelines (ETL, batch jobs, data processing) ko **Python code (DAGs)** se define karke schedule karta hai. MWAA matlab Airflow ka poora setup/servers AWS manage karta hai — aapko sirf DAGs likhne hain.

**Example — Amazon ka daily sales report:**
Amazon ko har subah 4 baje poore din ka sales report chahiye:
1. Data Engineer ek **DAG** likhta hai: "Subah 4 baje: raw sales data S3 se uthao → clean karo → Redshift me load karo → report email karo"
2. Airflow jo DAG ko **schedule** pe chalaata hai — har roz 4 AM
3. Har task ek step hai — agar ek fail ho to next task nahi chalega, retry hoga
4. Web UI pe pura graph, logs, status dikhta hai

**Airflow core concepts:**

- **DAG (Directed Acyclic Graph)** — aapki workflow ka blueprint, **Python file** me likha jata hai
- **Task** — ek kam ka unit (jaise: extract, transform, load)
- **Operator** — task ka type (PythonOperator, BashOperator, S3Operator, GlueJobOperator)
- **Schedule** — kab chalna hai (cron: `0 4 * * *`, every hour, etc.)
- **Scheduler** — DAG ko monitor karta hai aur schedule pe trigger karta hai
- **Worker** — actual tasks execute karta hai
- **Web Server (UI)** — DAGs, runs, logs dekhne ka dashboard
- **XCom** — tasks ke beech data share karna

**Example DAG:**
```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def extract(): ...
def transform(): ...
def load(): ...

with DAG('daily_sales_report', schedule='0 4 * * *', start_date=datetime(2026, 1, 1)) as dag:
    e = PythonOperator(task_id='extract', python_callable=extract)
    t = PythonOperator(task_id='transform', python_callable=transform)
    l = PythonOperator(task_id='load', python_callable=load)
    e >> t >> l   # order define karo
```

**MWAA key points:**
- **Fully managed** — Airflow setup, scaling, patching, HA — sab AWS sambhalta hai
- **S3 integration** — aapke DAGs aur plugins **S3 bucket** me store hote hain, MWAA wahan se pick karta hai
- **Environment sizes** — Small / Medium / Large (workers aur performance ke hisaab se)
- **Integrations** — S3, Glue, EMR, Lambda, Redshift, Athena, EC2, databases ke saath chalta hai
- **Security** — VPC me chalta hai, IAM roles se AWS services access karta hai
- **Auto-scaling** — workers load ke hisaab se automatically scale hote hain
- **Web UI** — Airflow console (Graph, Gantt, Tree, Logs) check kar sakte ho

**Setup steps:**

1. **S3 bucket banao** — jisme DAGs folder `dags/` aur requirements file hongi
2. **MWAA environment banao**
   - AWS Console → Amazon MWAA → "Create environment"
   - S3 bucket point karo, version pick karo (Airflow 2.x), size choose karo
   - VPC select karo (private subnets recommended), execution role banao
3. **DAGs upload karo** — DAG file banao, `s3://my-bucket/dags/` me upload karo
   ```bash
   aws s3 cp my_dag.py s3://my-bucket/dags/
   ```
4. **Web UI kholo** — MWAA console se "Open Airflow UI" → DAGs enable karo
5. **Environment update karo** (requirements/plugins add karne ke liye)

**MWAA vs Step Functions:**

| Feature | MWAA (Airflow) | Step Functions |
|---------|---------------|----------------|
| Definition | **Python code (DAGs)** | JSON/YAML state machine |
| Best for | **Data pipelines / ETL** (complex DAGs, dependencies) | Serverless app workflows (Lamba steps) |
| Scheduling | ✅ Cron, advanced (backfill, catchup) | ❌ (EventBridge se schedule karna padta hai) |
| Open source | ✅ (Apache Airflow, portable) | ❌ (AWS only) |
| UI | ✅ Full web UI (Graph, Logs, Calendar) | ✅ (Console pe graph) |
| Retry/error handling | Per-task retries | ✅ Built-in states |
| Pricing | Per environment per hour (always running) | Pay per execution |

**When to use MWAA:**
- **ETL / data pipelines** — S3 → Glue/Spark → Redshift → report
- **Complex dependencies** — hundreds of tasks, branching, backfills
- **Team already knows Python** — code-first approach chahiye
- **Open-source portability** — future me GCP/Azure pe bhi chahiye

**Pricing:**
- Per **environment size** per hour (jaise Small ~$0.45/hr, Medium ~$0.90/hr, Large ~$1.80/hr — approx)
- Data transfer + optional S3/CloudWatch costs alag
- Dhyaan rakho: environment 24x7 chalta hai — cost bhi 24x7 lagti hai

**Analogy:** MWAA = **Smart factory supervisor** — aap supervisor ko *instructions ki book* (Python DAGs) dete ho, wo har din same time pe workers se tasks karwata hai, fail hone par retry karta hai, aur report de deta hai. Aapko factory ki building (servers) khud nahi banana padti — MWAA ready-made factory deta hai.

## Data Processing & Analytics me S3 ka use

S3 sirf file storage nahi — wo modern **data lake** ka foundation hai. Saari raw data S3 me aati hai, wahi pe process hoti hai, aur analytics services S3 se **directly** query karti hain — koi extra database setup nahi.

**Example — Amazon ki sales analytics pipeline:**
```
Amazon App / Website
     ↓ (real-time events)
Kinesis Firehose ──→ S3 (Raw Zone: logs, clicks, orders)
                          ↓
                Glue (ETL: clean, transform, join)
                          ↓
                 S3 (Processed Zone: Parquet files)
                          ↓
        Athena (SQL query) ──→ QuickSight (dashboards)
```
1. Users ki har activity (click, order) real-time S3 me aati hai
2. **Glue** us raw data ko clean karta hai aur **Parquet** format me save karta hai
3. **Athena** SQL se queries chalata hai — bina kisi server/database ke
4. Sales team **QuickSight** pe live dashboard dekhti hai

**S3-based Data Lake architecture:**

S3 bucket me zones (folders) banao:
```
s3://data-lake/
├── raw/           → original data (jaisa aaya) — landing zone
├── processed/     → cleaned, transformed data (Parquet/Avro)
├── curated/       → business-ready tables (jo team use karti hai)
└── archive/       → old data (Glacier me move ho sakta hai)
```

**Partitioning (zaroori!):**
Data ko **partition karo** — query fast aur cost kam:
```
s3://data-lake/raw/orders/
├── year=2026/month=08/day=15/orders.csv
├── year=2026/month=08/day=14/orders.csv
```
- Athena/Glue partition ko read karta hai — sirf zaroori files scan hoti hain
- Bina partition: poori files scan hogi → **cost + latency zyada**
- Partition columns: year, month, day, country, category — jaise query hoti hai

**Consistent partition key naming** — `year=2026` (kabhi `2026` nahi) — warna Athena data nahi dikhata.

**S3 se directly query karne wali services:**

| Service | Kya karti hai |
|---------|---------------|
| **Athena** | Serverless SQL — S3 pe queries (CSV, JSON, Parquet) — pay per query (per scan) |
| **Glue** | Serverless ETL — Data Catalog + Crawlers — S3 data ko transform karta hai |
| **Glue Data Catalog** | S3 data ka metadata store — tables define karta hai |
| **Redshift Spectrum** | Redshift se S3 data SQL pe query — bina load kiye |
| **EMR** | Hadoop/Spark clusters — S3 se big data process karta hai |
| **QuickSight** | BI dashboards — S3/Athena data ko visualize |
| **Kinesis Firehose** | Real-time streaming data S3 me load (JSON/Parquet compress) |
| **S3 Select** | Sirf needed rows/columns read karo (S3 SDK se) — less scan, less cost |

**Data formats — kaunsa use karein?**

| Format | Readability | Size | Query Speed | Kab use karein |
|--------|--------|------|-------------|----------------|
| CSV | Easy to read | Large | Slow | Small data, human readable |
| JSON | Easy | Large | Slow | Nested data, logs |
| **Parquet** | Columnar | **~75% chhota** | **Fast** | Analytics default — actually use karo |
| Avro | Row-based | Chhota | Medium | Streaming/write-heavy pipelines |

**S3 + Lambda — event-driven processing:**
```
S3 me file aa gayi (upload) → Lambda trigger → file process → result S3/Athena me
```
- **S3 Event Notification** — `s3:ObjectCreated:*` → Lambda, SQS, SNS trigger
- Example: user photos S3 me aayi → Lambda resize karta hai → thumbnails alag folder me

**S3 + Kinesis — real-time streaming:**
```
IoT devices / app events → Kinesis Data Streams → (Lambda process) → S3
```
- Firehose S3 me **buffered** load karta hai (jaise 60 sec ya 128MB block me)
- Compress karo (gzip/Parquet) — storage aur Athena cost dono kam

**Cost optimization analytics me:**

- **S3 Intelligent-Tiering** — automatically hot ↔ infrequent data move — analytics data ke liye best
- **Lifecycle rules**:
  - 30 din baad → Standard → Infrequent Access
  - 90 din baad → Glacier (rarely accessed data)
  - 365 din baad → delete (ya Glacier Deep Archive)
- **Athena cost** — scan karein jitna kam, pay utna kam:
  - Parquet + partitioning use karo → 90%+ cost kam
  - `SELECT *` mat karo — sirf needed columns
  - Heavy queries ke liye **Redshift Spectrum** ya **EMR** sasta ho sakta hai

**Example — daily sales report (full pipeline):**
```
1. Orders table se data daily → S3 raw/orders/ (via Glue job ya Firehose)
2. Glue ETL: raw → processed/orders_parquet/ (Parquet + partition by date)
3. Athena: 
   "SELECT country, SUM(amount) 
    FROM orders_parquet 
    WHERE year=2026 AND month=8 
    GROUP BY country"
4. QuickSight → dashboard → business team
```

**Important points:**
- S3 = **single source of truth** — same data sab services use karti hain (Athena, EMR, Redshift, SageMaker)
- S3 pe **5000 GET/sec per prefix** limit — high throughput ke liye partition karo (random prefix)
- **Encryption** — SSE-S3, SSE-KMS — sensitive analytics data ke liye zaroori
- **Bucket policies** — sirf required IAM roles/services ko access do
- S3 **event notifications** se pura analytics ecosystem automate hota hai (file aayi → process → query)

**Analogy:** S3 data lake = **Giant warehouse** — saara saman (data) yahan rakha hai, kisi bhi safe ke bina. Athena = **Counter clerk** jo warehouse se sirf wo cheez laata hai jo aapne seekhi (query) — wo poori warehouse nahi uthata (partition + Parquet = clerk ko exact rack pata hai).

## EMR (Elastic MapReduce)

EMR AWS ka **managed big data processing** service hai jo **Hadoop ecosystem** (Spark, Hive, HBase, Flink, Presto) ko **EC2 clusters** pe chalti hai. Aapko clusters manually setup nahi karne padte — EMR EC2 instances launch karta hai, framework install karta hai, scale karta hai, aur khatam hone par band kar deta hai (auto-terminate).

**Example — Amazon ka 1TB sales data analysis:**
Amazon ke paas har din terabyte data aata hai (orders, clicks, inventory):
1. Data S3 me hai (raw/ folder)
2. Amazon **EMR cluster** launch karta hai (say 10x m5.xlarge + 1 master)
3. **Spark job** submit karta hai: "S3 se data padho → clean karo → trends nikalo → result wapas S3 me"
4. Cluster 1000+ cores pe parallel kaam karta hai — kuch minut me khatam
5. Job complete → cluster **auto-terminate** → sirf itne ka bill
6. Koi permanent server nahi — sirf job ke waqt cost

**EMR se kya kya kiya ja sakta hai:**

| Framework | Use Case |
|-----------|----------|
| **Spark** | Fast in-memory processing, ML pipelines, SQL (most popular) |
| **Hadoop MapReduce** | Traditional batch processing (disk-based) |
| **Hive** | SQL-like queries on huge datasets (data warehouse pe) |
| **HBase** | NoSQL database on top of HDFS |
| **Presto/Trino** | Interactive SQL on S3 data (fast queries) |
| **Flink** | Real-time stream processing |
| **Hue** | Web UI for Hive, SQL editor |

**Key concepts:**

- **Cluster** — 10-100s EC2 instances ka group jo ek saath kaam karta hai
- **Master node** — cluster ka "brain" — tasks coordinate karta hai, kind daata jobs
- **Core nodes** — data store (HDFS) + computation — naya data yahan aa sakta hai
- **Task nodes** — sirf computation, optional scale-up (spot instances ke liye best)
- **EMRFS** — EMR + S3: HDFS ki jagah S3 ko data store banao (sasta + durable)
- **Bootstrap actions** — cluster start hote waqt custom setup (packages install karo)
- **Steps** — ek cluster pe multiple jobs order me chalana
- **Auto-scaling** — load ke hisaab se nodes add/remove
- **Notebooks** (EMR Studio) — Jupyter-style development, interactive queries

**Cluster nodes diagram:**
```
EMR Cluster
├── Master Node (1) — resource manager, job tracker, web UI
├── Core Node (2-20) — Spark executors + HDFS/EMRFS data
└── Task Node (0-20, optional) — pure compute, spot pe sasta
        ↓
All nodes S3 se data padhte/likhte hain (EMRFS)
```

**EMR vs Glue vs Athena — kab kya use karein?**

| Feature | EMR | Glue | Athena |
|---------|-----|------|--------|
| Type | Managed clusters (EC2) | Serverless ETL | Serverless SQL |
| Best for | **Complex transformations**, ML, custom code (Spark) | ETL pipelines (crawl → transform → load) | Quick ad-hoc SQL queries |
| Languages | Python (PySpark), Scala, Java, SQL | Python (Spark-based) | SQL only |
| Control | ✅ Full control (cluster tuning) | Medium (jobs defined) | ❌ (query only) |
| Cost | Per node per second | Per job execution | Per data scanned |
| Setup time | Cluster launch ~5-15 min | Job definition | Immediate |
| When | 100GB+ heavy jobs, ML, custom logic | Repeatable ETL pipelines | Explore/verify data fast |

**EMR vs Redshift:**

| Feature | EMR | Redshift |
|---------|-----|----------|
| Purpose | General big data processing (any transformation) | Data warehouse (SQL analytics) |
| Data format | Raw/any (CSV, JSON, Parquet) | Columnar-stored tables |
| Best code | Spark/MapReduce | SQL |
| Latency | Minutes (batch) | Sub-seconds (BI queries) |
| Use case | Process first | Query fast repeated queries |

**Cost optimization — Spot instances (important!):**
- EMR nodes **Spot instances** pe chalao → **50-70%+ save** (task nodes ke liye best)
- Core/master pe **On-Demand** rakho (data loss se bachne ke liye)
- EMR vs EC2 khud: EMR ka **per-node premium** (~$0.02-0.12/hr per instance) — but setup, software, scaling, monitoring included

**Setup steps:**

1. **S3 data lake ready rakho** — raw data `s3://data-lake/raw/` me
2. **EMR cluster banao**
   - AWS Console → EMR → "Create cluster"
   - Choose release (EMR 7.x) + applications (Spark, Hive...)
   - Instance types (master: m5.xlarge, core: m5.xlarge), count (`start 4, max 10`)
   - **Region & VPC** select karo, **IAM role** attach karo (EC2 → S3 access)
3. **Job submit karo**:
   ```bash
   # Spark job (EMR on EKS/EMR CLI pe)
   aws emr add-steps --cluster-id j-XXXX --steps Type=Spark,Name=ETL,ActionOnFailure=CONTINUE,Args=[s3://scripts/etl.py,--deploy-mode,cluster]
   ```
   Ya EMR Studio notebook se PySpark chalado
4. **Monitor karo** — EMR console pe cluster health, step logs, Spark UI
5. **Terminate karo** (ya `--auto-terminate` set karo) — job ke baad khud band ho jaye
6. **S3 me result check karo** — output `s3://data-lake/processed/`

**EMR + S3 = data lake processing standard:**
```
S3: raw/ (CSV/JSON logs)
   ↓
EMR (Spark):
   - read from S3
   - clean/transform/aggregate
   - write results → back to S3 (Parquet)
   ↓
S3: processed/
   ↓
Athena / Redshift Spectrum / QuickSight → analytics
```

**Important points:**
- **ETL pipelines** — Glue easier hai, EMR jab full control chahiye
- **Auto-terminate** use karo — bhool se cluster chalte rehne pe costly bill
- **Idle cluster** = idle bill — 15 min no jobs → terminate
- Long-running clusters: only jab predictable recurring jobs hain
- Security: cluster ko **private subnet** me rakho, security groups tight karo
- **EMRFS + S3 versioning** — accidental delete se data safe

**Pricing:**
- **EC2 instance cost** (per second) + **EMR premium** per node
- Spot = 50-70% sasta
- Region, instance type ke hisaab se change hota hai

**Analogy:** EMR = **Heavy machinery rental company** — aapko sirf kaam ke liye 10 cranes (nodes) chahiye? Rent karo 2 din ke liye, kaam karo, wapas de do. Cranes khareedne (permanent servers) ki zaroorat nahi. Spot instance = thodi si choti cranes jo aur sasti hain lekin kabhi bhi waapas maangi ja sakti hain.

## Athena

Athena AWS ka **serverless SQL query** service hai jo **directly S3 pe data query** karta hai — koi server, koi database setup, koi cluster nahi. Bas S3 me data hai, Athena pe SQL likho, result aapko mil jata hai. **Pay per query** (jo data scan hua uspe).

**Example — Sales team ka quick query:**
Amazon ki sales team poochti hai: "Kolkata me is hafte kaunsa product sabse zyada bika?"
1. Data S3 me hai (orders/ ka Parquet) — koi database nahi, koi move nahi
2. Team Athena pe SQL likhti hai:
   ```sql
   SELECT product_name, SUM(quantity) 
   FROM orders 
   WHERE city = 'Kolkata' AND week = 33 
   GROUP BY product_name 
   ORDER BY SUM(quantity) DESC 
   LIMIT 10;
   ```
3. Athena S3 ke sirf zaroori files scan karta hai (partitioning + Parquet)
4. Seconds me result — bill sirf scanned data ke liye (~$5/TB)

**Athena key features:**
- **Serverless** — kuch bhi provision nahi karna, zero management
- **S3 pe directly** — data kisi database me load nahi karna padta
- **Standard SQL** — ANSI SQL queries (jo developers/analysts already jante hain)
- **Pay per query** — jo data scan hota hai uspe ($5.00/TB) — no idle cost
- **Fast** — partition + columnar format (Parquet) pe milliseconds-second me
- **Formats** — CSV, JSON, Parquet, Avro, ORC, log files, Glue tables
- **Integrations** — Glue Data Catalog (tables), QuickSight (BI), Lambda (automation)

**Athena kaise kaam karta hai?**

```
S3 (data) → Glue Data Catalog (table definitions) → Athena (SQL) → Results
     ↑                                                  ↓
  Uska Data Crawler se table banao                  (S3 me results save
                                                     ho jate hain)
```

1. **Glue Data Catalog** me table banata hai (manual ya **Crawler** se) — batata hai: "ye S3 files ka structure kya hai"
2. Athena query chalata hai
3. Query engine files scan karta hai → SQL apply → result
4. Results default S3 me save hote hain (aur CSV download)

**Athena vs Redshift vs EMR — kab kya:**

| Feature | Athena | Redshift | EMR (Spark) |
|---------|--------|----------|-------------|
| Type | Serverless SQL | Data warehouse (cluster) | Big data clusters |
| Setup | 🔥 None — bas SQL | Cluster provision | Cluster launch |
| Cost | Per query (scan) | Per cluster (24x7) | Per node per sec |
| Best for | Ad-hoc queries, exploration | BI dashboards, repeated queries | Complex transformations, ML |
| Data location | S3 (no copy) | Loaded into Redshift | S3/ HDFS |
| Users | Analysts, developers | BI teams | Data engineers |

**Answer:**
- **Athena:** Kuch queries karni hain? Data S3 me hai? → Athena
- **Redshift:** Sales team roz dashboards chalayegi, consistent fast response → Redshift (data load karke)
- **EMR:** Complex Spark job (ML, heavy transformation) → EMR

**Setup & use steps:**

1. **Data S3 me rakho**: `s3://data-lake/processed/orders/` — Parquet + partitions (year=2026/)
2. **Table banao** (2 tarike):
   - **Manually:** Athena Console → "Create table" → S3 path, format, columns
   - **Glue Crawler (recommended):** Crawler → S3 path scan → automatically table banaye
3. **Query chalao:**
   ```sql
   SELECT year, SUM(total) FROM orders WHERE year = 2026 GROUP BY year;
   ```
4. **Results dekh lo** — Athena Console me ya S3 se CSV
5. **QuickSight se connect karo** — visual dashboards

**Partitioning practice:**
```sql
-- Partitioned table ke saath WHERE me partition column zaroor do
SELECT * FROM orders WHERE year = 2026 AND month = 8;   -- ✅ fast, kam scan
SELECT * FROM orders;                                   -- ❌ full scan, zyada cost
```

**Important points:**
- **Scan cost** — `SELECT *` se bacho; sirf columns chuno
- **Partition + Parquet** = 90% cost kam
- Table names chhote karo (lowercase) — schema change par issue ho sakta hai
- **Default result location** S3 me set karo — results kahan save honge
- Query time limit: **30 min** default (workgroup se adjust)
- Data catalog — AWS Glue **free tier** me bhi use hota hai (tables)
- Athena **schema-on-read** — data S3 me change karo, table update karo

**Pricing:**
- **$5 per TB scanned** ($10/6 = ~$5 for PB-scale)
- Partitioned/compressed data = kam scan = kam cost
- Cheap example: 10MB scan = $0.00005, Koi query ~$0.01

**Analogy:** Athena = **Google search for your S3 data** — Google ka server kharidne ki zaroorat nahi, bina software ke. Bas search (SQL) likho, results milengaye. Jaise search engine poori web ko scan karta hai, waise Athena S3 ko — sirf wo pages scan karta hai jo query se match hote hain (partitioning + Parquet).

## Glue (AWS Glue)

Glue AWS ki **serverless ETL (Extract, Transform, Load)** service hai. Ye S3/databases se data **extract** karta hai, **transform** (clean, join, aggregate) karta hai, aur processed data wapas S3 ya kisi target me **load** karta hai. Ye **Apache Spark** based hai — Glue server manage karta hai, aap sirf job likhte ho.

**Example — Amazon ka customer data pipeline:**
Amazon ko 5 alag sources (MySQL orders, MongoDB users, S3 logs) ka data ek jagah chahiye:
1. **Glue Crawler** har source scan karta hai → **Data Catalog** me tables banata hai
2. **Glue ETL job** (Python/PySpark) likha jata hai: 5 sources → join → clean (nulls remove, format fix) → Parquet
3. Job schedule karo (har raat 2 baje) ya event pe trigger (S3 me file aayi)
4. **Glue Studio** me drag-drop ya code se job banao
5. Clean data S3 processed zone me jaata hai → Athena/QuickSight ready

**Glue ka pura ecosystem (4 parts):**

| Part | Kya karta hai | Kya use hota hai |
|------|---------------|------------------|
| **Data Catalog** | Metadata store — tables, schemas, partitions | Athena, Redshift Spectrum, EMR sab isi se tables lete hain |
| **Crawler** | Data source scan karta hai, schema detect karta hai, tables banata hai | Naya data aane par table auto-create |
| **ETL Jobs** | Serverless Spark jobs — transform+load | Main work: clean, join, aggregate, convert formats |
| **Triggers & Workflows** | Jobs schedule/coordinated chalana | Daily pipelines, dependency chains |

**Glue Crawler + Data Catalog example:**
```
S3: s3://data-lake/raw/orders/year=2026/month=8/
        ↓ (Glue Crawler scan)
Glue Data Catalog: table "orders" (columns: order_id, amount, city) + 2 partitions
        ↓
Athena / Redshift Spectrum / EMR — isi table ko query karte hain
```

**ETL Job example (PySpark):**
```python
import sys
from awsglue.transforms import *
from awsglue.context import GlueContext
from pyspark.context import SparkContext

sc = SparkContext()
glueContext = GlueContext(sc)

# 1. Extract — Data Catalog se table padho
orders = glueContext.create_dynamic_frame.from_catalog(
    database="analytics", table_name="orders_raw")

# 2. Transform — clean & filter
clean = orders.filter(lambda row: row["amount"] is not None) \
              .drop_fields(["temp_col"])

# 3. Load — Parquet me S3 processed zone
glueContext.write_dynamic_frame.from_options(
    frame=clean, connection_type="s3",
    connection_options={"path": "s3://data-lake/processed/orders/"},
    format="parquet")
```

**Glue vs EMR vs Athena — kab kya use karein?**

| Feature | Glue | EMR | Athena |
|---------|------|-----|--------|
| Type | **Serverless ETL** | Managed Spark clusters | Serverless SQL |
| Code likhoge | PySpark jobs | PySpark, Scala, Java, Hive | SQL only |
| Best for | Repeatable ETL pipelines, Crawlers | Complex ML, heavy custom processing | Quick ad-hoc queries |
| Setup | Serverless — job likho, chal gaye | Cluster launch (~min) | Kuch nahi (table checked hota hai) |
| Cost | Per job execution (DPU-hours) | Per node per sec + premium | Per TB scanned |
| Extra | Data Catalog + Crawlers included | Full control, notebooks | Best with Glue Catalog tables |

**Glue ETL cost:** **DPU (Data Processing Unit)** me count hota hai — 1 DPU = 4 vCPU + 16GB RAM. ~$0.44/DPU-hour. Job performance pe cost base hota hai.

**Glue alternatives:** 
- **EMR** = aap khud cluster chalao, full control
- **Spark on EKS/Fargate** = containerize karo job
- Glue best jab: serverless simplicity + Data Catalog ek jagah chahiye

**Setup steps (Crawler + Job):**
1. Console → AWS Glue → **Crawlers** → Create → S3 path (source) → role → run
2. Data Catalog me table check karo
3. **Jobs** → Create → Visual (Glue Studio) ya Python (Spark)
   - Job type: Spark, language: Python
   - IAM role: S3 read/write + catalog access
4. **Schedule** — Trigger (cron) ya event (S3) se job chalao
5. Job run → S3 processed zone → verify Athena pe

**Analogy:** Glue = **Data factory workers** — kaam pe aate hain kabhi khud nahi. Andaaz: Data Catalog = **chhati (shelf list)** jahan har cheez ka location likha hai. Crawler = **Scanner** jo naya saman chhati pe register karta hai. ETL Job = **Factory workers** jo saman ko clean-pack karke ready kar dete hain.

## Redshift

Redshift AWS ka **petabyte-scale data warehouse** hai — **columnar storage** + **massive parallel processing (MPP)** se SQL analytics **bohot fast** chalata hai. Ye **BI queries / dashboards** ke liye hai — jahan Athena/EMR se baar baar query karna slow ho.

**Example — Amazon ki BI dashboard:**
Amazon ki sales team ko live dashboard chahiye — roz 2GB data update hota hai, 500 employees query karte hain:
1. Data S3 se **COPY** karke Redshift me load hota hai (daily)
2. Queries (SELECT, GROUP BY, JOINs) **parallel** 100s cores pe chalti hain
3. Dashboard **sub-second** response deta hai — Athena yahan slow badega (har baar scan)
4. **Concurrency Scaling** — 500 log ek saath aaye to extra clusters auto-launch

**Redshift architecture:**
```
Redshift Cluster
├── Leader Node — query plan + distribute to compute nodes
├── Compute Nodes (2-100s)
│   └── Slices (per node) — data partitioned + parallel queries
└── Columnar Storage (RA3 / DC2) — compressed, fast scans
        ↓
    S3 (COPY/UNLOAD) — data lake integration
```

**Key features:**
- **Columnar storage** — sirf needed columns read hoti hain, compression zyada (CSV se 75%+ chhota)
- **MPP (Massive Parallel Processing)** — query har node/slice pe parts me chalti hai → fast
- **Redshift Spectrum** — S3 pe bhi SQL query (bina load kiye) — Redshift + S3 dono milakar
- **Serverless (option)** — cluster khud manage nahi karna, pay per query (2023+)
- **AWS integrations** — S3 COPY/UNLOAD, Glue, DMS (data migration), QuickSight BI
- **Auto scaling** — compute nodes add/remove load ke hisaab se

**Redshift vs Athena — kab kya:**

| Feature | Redshift | Athena |
|---------|----------|--------|
| Type | Data warehouse (cluster) | Serverless query engine |
| Best for | **Repeated BI queries, dashboards** | Ad-hoc, occasional queries |
| Data location | Loaded (columns in cluster) | S3 pe hi |
| Query speed | **Sub-second** (prepared) | Seconds-minutes (scan) |
| Setup | Cluster provision + load data | None (table checked hota hai) |
| Cost | Cluster 24x7 (ya serverless per query) | Per TB scanned |
| Concurrency | High (100s queries) | Limited (workgroup me) |
| When | Corporate BI, Tableau/QuickSight, slow Athena bardaste nahi | Exploration, event-driven SQL |

**Setup steps:**
1. Console → Redshift → **Create cluster** (provisioned ya serverless)
   - Node type (RA3.xlarge), node count, admin password, VPC
2. **Data load:**
   ```sql
   COPY orders FROM 's3://data-lake/raw/orders/' 
   IAM_ROLE 'arn:aws:iam::123456789012:role/redshift-s3'
   FORMAT AS PARQUET;
   ```
   Ya **DMS** / **Glue** se continuous load
3. **Query chalao:**
   ```sql
   SELECT city, SUM(amount) FROM orders 
   WHERE year = 2026 GROUP BY city ORDER BY 2 DESC;
   ```
4. **QuickSight / Tableau** se connect karo → dashboards
5. **Spectrum** — S3 data bhi query karo (same Redshift):
   ```sql
   SELECT * FROM spectrum.orders_parquet WHERE month = 8;
   ```

**Redshift Spectrum vs Athena:**
- Both query S3 pe — Spectrum Redshift cluster se (fast, consistent)
- Athena behtar jab Redshift jitna cost justify nahi hota (kam queries)

**Cost:**
- **Provisioned:** On-demand per node ($0.25/hr - $0.90/hr approx, RA3) — cluster 24x7
- **Serverless:** Pay per query (RPU-hours) — no idle cost
- Reserved nodes = ~25-40% save (1-3 yr commit)
- **Snapshot + pause** — idle time pe cluster pause karo, cost 0

**Best practices:**
- **Sort key + Dist key** — table design se query fast: sort = WHERE filter column, dist = JOIN column
- **VACUUM + ANALYZE** — tombstones clean karo, stats update
- **Data compression** — columnar auto = kam disk
- **Load batching** — har row COPY mat karo, batch load karo (CTAS/COPY)
- **Spectrum** — rarely accessed data S3 pe rakho (sasta), hot data Redshift pe
- **Pause/resume** — night pe cluster pause — 30-40% cost save

**Analogy:** Redshift = **Supermarket with organized aisles** — ek baar saman shelves pe sajja diya (columnar + sort keys), customer ko har baar warehouse se dhundna nahi padta. Wahan se dhundna = Athena (S3, har baar scan). Shelf pe sajja hua saman = sub-second query. Redshift Spectrum = Shelf aur warehouse dono ka mix.

## QuickSight

QuickSight AWS ka **cloud-native BI (Business Intelligence)** service hai — jisse aap data pe **visual dashboards aur reports** banate ho. Ye Athena, Redshift, S3, Aurora, RDS, DynamoDB, etc. se connect hota hai aur **web browser** pe interactive charts/business insights deta hai. Ye **serverless** hai — koi server, koi table kharidne ki zaroorat nahi.

**Example — Amazon ki sales dashboard:**
Amazon ke sales manager ko live dashboard chahiye — "Aaj kya bik raha hai?":
1. Data Athena (S3 tables) se aata hai — ya Redshift/QuickSight SPICE storage me
2. QuickSight **SPICE** me data import karta hai (cache) — dashboard fast
3. Manager browser kholta hai → **dashboard** dekhta hai: charts, maps, KPIs
4. Charts interactive hain — filter karo, drill-down karo, click karo
5. Report email se **scheduled** share hoti hai

**QuickSight core concepts:**

- **Dataset** — data ka source (Athena table, Redshift query, S3 file, upload CSV)
- **SPICE** — QuickSight ka **in-memory cache engine** (Super-fast, Parallel, In-memory, Calculation Engine) — data SPICE me load karo, queries ~10x fast
- **Analysis** — charts, filters, calculations ka canvas — yahan dashboard design hota hai
- **Dashboard** — published analysis (read-only, shareable, scheduled email)
- **Visuals** — bar, line, pie, map, table, Pivot, KPI cards, etc.
- **Calculated fields** — custom formulas (SUM, AVG, IF, date functions)
- **ML Insights** — auto forecasting, anomaly detection, narratives
- **Data Refresh** — SPICE data schedule pe refresh (hourly/daily) — refresh karna

**QuickSight ke saath kaunse sources:**

| Source | Use Case |
|--------|----------|
| **Athena** | S3 data lake pe dashboard (most common) |
| **Redshift** | Data warehouse BI (dashboards + SPICE) |
| **RDS/Aurora** | Database pe direct dashboards |
| **S3** | Files direct upload (CSV, Excel, JSON) |
| **DynamoDB** | NoSQL data analytics |

**Architecture flow:**
```
Data Source (Athena/Redshift/S3) 
     ↓ (query / SPICE import)
QuickSight (Dataset → Analysis → Dashboard)
     ↓ (share)
Web Browser / Mobile App / Email report / Embedding
```

**Setup steps:**
1. **Login/Account** — QuickSight console → sign up (email + IAM role)
2. **Dataset banao**: 
   - Data sources → Athena select karo → database/table chuno
   - Ya S3 upload / Redshift connection
   - SPICE me import karo (fast) ya direct query (live)
3. **Analysis banao**: 
   - "New analysis" → dataset attach
   - Add visuals (charts) drag-drop: fields → axes
   - Filters, calculated fields, forecast add karo
4. **Dashboard publish karo**: 
   - "Publish dashboard" → name + access (users)
   - Share link, email schedule, embed in web app
5. **Data refresh** (SPICE): schedule set karo (daily/hourly)

**Example query (Athena source):**
```sql
-- QuickSight se Athena ke data pe:
SELECT day, SUM(sales) AS total_sales 
FROM sales_table 
GROUP BY day ORDER BY day;
-- QuickSight isse chart bana dega — bina custom SQL ke bhi (visual builder)
```

**Interactive dashboard features:**
- **Filters** — date range, city, category — click aur dropdown se
- **Drill-down** — country → state → city (hierarchy)
- **Parameters** — users kuch dabaye to filter change
- **Actions** — chart click → dusre chart filter
- **Email reports** — scheduled (daily subah 8 baje dashboard email)

**QuickSight vs Tableau/Power BI:**

| Feature | QuickSight | Tableau/Power BI |
|---------|-----------|------------------|
| Cloud native | ✅ AWS-native, Athena/Redshift native connect | Ekdum connected but setup extra |
| Pricing | **Per user subscriptions** (~$10-24/user/mo) | License (Tableau costly) |
| SPICE | ✅ In-memory fast | ❌ (external servers) |
| Serverless | ✅ | Mixed (self-hosted options) |
| When | AWS stack pe best | Enterprise/sales teams, complex viz |

**Pricing:**
- **Author** (~$24/user/mo) — analysis bana sakta hai
- **Reader** (~$5/user/mo) — sirf dashboards dekh sakta hai
- Free trial 60 din — small team
- data refresh cost + SPICE capacity extra (SPICE: 10GB default)

**Analogy:** QuickSight = **Dashboard builder for your data lakes** — jaise Tableau-Power BI — bas aap baith jao, charts drag-drop, sab ready. SPICE = **pre-cooked ingredients** — query har baar se jaldi, kyunki data pehle se cached hai. Athena/Redshift = **raw kitchen** — har order pe fresh — dashboard ke liye slow.

## Kinesis

Kinesis AWS ka **real-time data streaming** services ka family hai — jo **live data** (events, clicks, logs, IoT sensor data) ko **continuous** collect, process aur analyze karta hai. Socho: database ke liye S3 = offline storage, Kinesis = **live river of data** jo continuously behta hai.

**Example — Amazon ka live click-stream analytics:**
Amazon pe har second 1000s clicks aate hain (products, cart, search):
1. Website/app events **Kinesis Data Streams** me daalti hai (real-time)
2. **Kinesis Data Firehose** data ko **S3/Redshift** me **buffered load** karta hai (60s/128MB block)
3. **Lambda/Kinesis Data Analytics** live processing karta hai — "abhi trending kya hai?"
4. Analytics team **real-time dashboard** dekhte hain — sirf minutes delay

**Kinesis ek family hai — 4 services:**

| Service | Kya karta hai | Use case |
|---------|--------------|----------|
| **Kinesis Data Streams** | Real-time data streams (raw ingest, producers→consumers) | Live data pipeline-backend |
| **Kinesis Data Firehose** | Data ko destination (S3, Redshift, OpenSearch) pe buffered load | Streaming→storage (ETL-lite) |
| **Kinesis Data Analytics** | Real-time SQL/Flink analysis pe streams | Live dashboards, alerts |
| **Kinesis Video Streams** | Video streams (live CCTV, drone feeds) | Video analytics, ML |

**Kinesis Data Streams — core concepts:**

- **Stream** — ordered sequence of records (like a never-ending log)
- **Shard** — stream ki unit (parallelism) — data shards par distribute hota hai
  - 1 shard = 1MB/s write, 2MB/s read, ~1000 records/sec
  - Stream 5 shards → 5x throughput
- **Producer** — data daalne wala (app, IoT device, SDK)
- **Consumer** — data padhne wala (Lambda, EC2 app, Kinesis Analytics)
- **Record** — data ka unit: partition key + sequence number + data blob
- **Retention** — data stream me default **24 hours** (max 365 days) — processed nahi to expire
- **Partition key** — decides konsa shard data jayega (hash-based)

```
Producers (app/IoT/clickstream)
     ↓  PutRecord API (SDK/agents)
Kinesis Data Streams (1+ shards) — 24h retention
     ↓  Consumers subscribe
Lambda / Kinesis Data Analytics / EC2 / Firehose → S3
```

**Kinesis Data Firehose:**
- **Ingest → transform → load** — most users ke liye simplest
- Destinations: **S3, Redshift, OpenSearch, Splunk, HTTP endpoint**
- **Buffering:** batch load (jaise 60-900 sec ya 1-128MB) — kam calls, kam cost
- **Transform:** Lambda (compression, format convert) + **data format conversion** (Parquet)
- Auto-scaling, managed — bas source connect kar do

```
Streaming data → Firehose (buffer 60s/128MB) → S3 (Parquet/JSON) → Athena/Redshift
```

**Kinesis Data Analytics:**
- Real-time queries/SQL pe live streams (**KDA Studio** — SQL/Flink)
- Example: "Har 5 minute me count clicks per product — threshold cross to alert"
- Output → S3, destination, Kinesis stream

**Kinesis vs SQS:**

| Feature | Kinesis Data Streams | SQS |
|---------|---------------------|-----|
| Purpose | **Real-time streaming, multiple consumers, replay** | Simple message queue (decoupling) |
| Ordering | ✅ Partition key se (ordered replay) | Best-effort (FIFO ordered) |
| Multiple consumers | ✅ (independent, replay-se kar sakta) | ❌ (1 consumer reads) |
| Retention | 24h - 365 days (replay possible) | 4 days max |
| Throughput | MB/s per shard (scale-up shards) | High - unlimited-ish |
| Use case | Clickstream, IoT, live dashboards | Task queues, async processing (orders) |

**Kab kya use karein?**
- **Kinesis:** Real-time data (millions events), replay zaroori, multiple consumers, live analytics
- **SQS:** Simple async task/queue, one consumer, no streaming needs

**Kinesis vs EventBridge:**
- **EventBridge:** Event-driven (fire-and-forget, "ye hua to ye karo") — event bus + rules
- **Kinesis:** Continuous streaming data — ordered, replayable, high volume

**Setup steps — Data Streams + Lambda:**

1. **Stream banao**
   - Console → Kinesis → Data Streams → "Create stream"
   - Name + shard count (small: 1-2, high: 10+)
2. **Producer data daalo** (SDK ya Firehose):
   ```bash
   # AWS CLI example
   aws kinesis put-record \
     --stream-name click-stream \
     --partition-key "user-123" \
     --data "product_id=iphone15&action=view"
   ```
3. **Consumer/Processor attach karo**
   - Kinesis → stream → "Create consumer" (Lambda/EC2)
   - Ya Firehose → S3 ka delivery stream banao
4. **Monitor karo** — CloudWatch: shard-level metrics, IteratorAge, Records
5. **Scale:** shards add karo (hot partition → partition key socho)

**Important points:**
- **Shard capacity** — 1 shard = 1MB/s write, 2MB/s read — scale = shards barhao
- **Partition key design:** unbalanced key = one shard hot — throughput bottleneck
- **Records expire** (default 24h) — consumer se pade nahi to kha gaye
- **At-least-once** — duplicate records ho sakte hain — consumer idempotent likho
- **Enhanced Fan-Out** — many consumers ko low latency (2MB/s per consumer)
- Kinesis + Lambda = **serverless streaming** — no server management
- **Cost:** per shard-hour (~$0.015/shard-hr) + data PUTs (~$0.014/1000 PUTs)
- Firehose: per GB ingested (~$0.029/GB)

**Analogy:** Kinesis = **Amazon's conveyor belt** — 24x7 data calatay rehta hai. Data Streams = **Belt** — saman belt par chalta hai (records on shards), workers (consumers) belt se utthate hain; agar koi miss ho gaya to 24h tak belt fix se milega (replay). Firehose = **Boxing station** — belt se data uthakar box (S3/Redshift) me pack karta hai. Data Analytics = **Live CCTV operator** — belt par basi cheez se turant report/alert banata hai.

## CloudWatch (Monitoring & Logging)

CloudWatch AWS ki **monitoring and observability** service hai — jo aapke AWS resources (EC2, Lambda, S3, databases) ka **health, performance aur logs** track karta hai. Ye 3 main cheezein karta hai: **Metrics** (numbers), **Alarms** (alerts), **Logs** (text logs) — aur un sab pe **dashboards** deta hai.

**Example — Amazon ka EC2 server monitor:**
Amazon ka ek web server EC2 pe chalta hai:
1. **CloudWatch Metric** har 1 min me collect karta hai: CPU %, memory, disk, network
2. **Alarm** set hai: "CPU > 80% for 5 min" → **SNS** se team ko email
3. Agar alarm hua → team turant response deti hai (ya Auto Scaling instance badha deta hai)
4. **Logs** — app ki logs (errors, requests) CloudWatch Logs me jaati hain
5. **Dashboard** — sab kuch ek screen pe live

**CloudWatch ka pura ecosystem (components):**

| Component | Kya karta hai |
|-----------|---------------|
| **Metrics** | Numeric data (CPU, latency, errors) — services auto-report karti hain |
| **Alarms** | Metric threshold cross → action (SNS email, Auto Scaling, Lambda) |
| **Logs** | App/system logs collect karta hai — searchable, S3 export |
| **Log Insights** | SQL-like queries logs pe — errors dhundna |
| **Dashboards** | Metrics + logs ek visual screen pe |
| **Events / EventBridge** | Resource change pe events → triggers |
| **Synthetics** | Canaries — health checks (URLs pe traffic bhejta hai) |
| **X-Ray (separate)** | Distributed tracing — request paths |

**1. CloudWatch Metrics:**
- **Automatic** — EC2, Lambda, RDS, ALB, S3... sab basic metrics bhejte hain (free)
- **Custom metrics** — apne app ke metrics bhejo (SDK/CLI)
- **Resolution:** Standard (1 min) | Detailed (1 sec, extra cost)
- Example metrics:
  - **EC2:** CPUUtilization, NetworkIn/Out, DiskRead/Write
  - **Lambda:** Invocations, Duration, Errors, Throttles
  - **ALB:** HealthyHostCount, RequestCount, TargetResponseTime

```bash
# Custom metric bhejna (example)
aws cloudwatch put-metric-data --namespace "MyApp" \
  --metric-name "OrderProcessingTime" --value 1.24 --unit Seconds
```

**2. CloudWatch Alarms:**
- Threshold + period set karo: "CPU > 85% for 3 consecutive periods"
- States: **OK → ALARM → INSUFFICIENT_DATA**
- Actions:
  - **SNS** — email/SMS/Slack (notification)
  - **Auto Scaling** — instances add/remove
  - **EC2 action** — stop/reboot/terminate
- Costs: $0.10/alarm/month — har metric alarm pe

```json
// Alarm example: Lambda errors
{
  "MetricName": "Errors",
  "Namespace": "AWS/Lambda",
  "Statistic": "Sum",
  "Period": 60,
  "EvaluationPeriods": 5,
  "Threshold": 10,
  "ComparisonOperator": "GreaterThanThreshold",
  "AlarmActions": ["arn:aws:sns:us-east-1:123456789012:ops-alerts"]
}
```

**3. CloudWatch Logs:**
- EC2: **CloudWatch Agent** install karo → /var/log/ logs collect
- Lambda: `print`/`console.log` automatically logs pe jaati hain (native)
- ECS/Fargate: container logs — awslogs driver se
- **Log Groups** — logs ka folder (jaise `/aws/lambda/my-function`)
- **Log Streams** — ek container/instance ke logs (inside group)
- **Retention** — set karo (1 day - 10 years) — cost control
- **Export** — S3 pe export, Kinesis/S3 me streaming

```bash
# Logs dekhna
aws logs filter-log-events --log-group-name /aws/lambda/my-function \
  --filter-pattern "ERROR" --limit 20

# Log group banao
aws logs create-log-group --log-group-name /my-app/web
```

**4. CloudWatch Logs Insights:**
- Query logs me SQL — fast search (logs pe):
```sql
-- Last 20 errors wali logs
fields @timestamp, @message
| filter @message like /(?i)error/
| sort @timestamp desc
| limit 20
```

```sql
-- Lambda function: Kitni baar galat hua
stats count(*) by @message
| filter @message like /ERROR/
| sort count desc
| limit 10
```

**5. CloudWatch Dashboards:**
- Multiple metrics ek screen pe — drag-drop widgets
- **Automatic dashboards** — service review me ready-made
- Share karna (HTML/JSON), embedded dashboards
- Alerts ke liye color coding — red = alarm

**Setup steps — EC2 monitoring:**
1. **CloudWatch Agent install** (server metrics + logs):
   ```bash
   wget https://s3.amazonaws.com/amazoncloudwatch-agent/.../amazon-cloudwatch-agent.deb
   sudo dpkg -i amazon-cloudwatch-agent.deb
   ```
   Configuration: CPU, memory, disk + `/var/log/app.log` include karo
2. **IAM role** attach karo (CloudWatchAgentServerPolicy) 
3. **Alarm banao:** Console → CloudWatch → Alarms → "Create alarm" → metric chuno → threshold → SNS topic
4. **Dashboard banao:** Dashboards → widgets → metrics + logs
5. **Insights query logs pe** — troubleshooting

**CloudWatch vs X-Ray vs Prometheus/Grafana:**

| Feature | CloudWatch | X-Ray | Prometheus/Grafana |
|---------|-----------|-------|--------------------|
| Type | AWS-native monitoring | Distributed tracing | Open-source monitoring (self-host) |
| Metrics | ✅ (AWS-wide) | Tracing only | App metrics |
| Logs | ✅ | ❌ | External (Loki) |
| Distributed tracing | ❌ (connect wala) | ✅ | Jaeger/Zipkin |

**Important points:**
- Free tier: **10 custom metrics** + 5GB logs — basic monitoring free
- **Cost control**: retention chhota rakho, no `*` queries, log volume dekhna
- **Alarms cost** ~$0.10/alarm — 100 alarms = $10/mo
- **Permissions** — IAM role me CloudWatchLogsFullAccess / CloudWatchAgentServerPolicy dena
- Instance metrics (memory/disk) **by default nahi** — agent lagao
- **Status checks** (EC2) — system/instance health — separate (free)
- **Container Insights / Lambda Insights** — advanced service metrics (extra cost)

**Analogy:** CloudWatch = **Hospital ICU monitor** — har patient (server) ka ECG ya CPU, heart rate (alarms), aur nurses logs (log files) sab kuch ek monitor pe. Alarm = **Emergency buzzer** — heart rate zyada hua to nurse ko call. Dashboards = **ICU room display** — doctor ko sab ICU stats ek jagah milta hai.

## CloudTrail (Audit & Security)

CloudTrail AWS ki **audit logging** service hai — jo aapke AWS account me **har API call** (jo koi bhi aapke account me karta hai) ko record karti hai. Ek tarah se **CCTV footage of AWS** — "kaun, kab, kya kiya, kahan se" — security, compliance aur troubleshooting ke liye.

**Example — "Mere S3 bucket kya koi delete kiya?"**
1. Koi hacker (ya employee) S3 bucket delete kar deta hai
2. Aap **CloudTrail** kholte ho → us S3 bucket ke events search karte ho
3. Milta hai: `DeleteBucket` event — **kaun IAM user**, **kis IP se**, **kab** (timestamp)
4. Ye event **CloudWatch Logs** me bhi ja sakta hai → alarm trigger — "delete hua!"
5. **Security team** incidents investigate karti hai — proof ke saath

**CloudTrail kya record karta hai?**
- **Management events** (default, free) — console/login, IAM changes, bucket created/deleted, config changes
  - **Read** events — jo cheeze dekhna (list, get)
  - **Write** events — jo badalna (create, delete, update)
- **Data events** (extra cost) — S3 object-level (GetObject, DeleteObject), Lambda invocations
- **Insight events** — unusual activity detection (API call unusual patterns)

**CloudTrail components:**

| Concept | Kya hai |
|---------|---------|
| **Trail** | Logging configuration — kya log karna hai, kahan store karna hai |
| **Event history** | Last 90 days ka log — console se visible (free, searchable) |
| **S3 delivery** | Events S3 bucket me durable store (JSON) — long-term retention |
| **CloudWatch Logs** | Events CloudWatch Logs me → alarms/monitoring |
| **Multi-region trail** | Saare regions ki activity ek jagah |
| **Organization trail** | Poori AWS Organization ki activity ek trail me |

**Trail types:**

| Type | Scope | Use Case |
|------|-------|----------|
| **Management events** | Console/sign-in, IAM, resource changes | Default security audit |
| **Data events** | S3 objects, Lambda calls | High-volume — object tracking |
| **Insight events** | Abnormal API usage | Anomaly detection |

**CloudTrail vs CloudWatch:**

| Feature | CloudTrail | CloudWatch |
|---------|-----------|------------|
| Purpose | **Audit** — kaun kya kiya (API activity) | **Monitoring** — system health (metrics) |
| Records | API calls, user actions | Metrics, logs, alarms |
| Who | IAM actions, events | Infra health |
| When | Security/compliance | Ops/performance |
| Data | JSON event files (S3) | Logs + metrics |

- **CloudTrail:** "S3 bucket kholne ka kaun aaya" — security
- **CloudWatch:** "Mera server slow kyu" — operations

**Setup steps:**

1. **Trail banao** — Console → CloudTrail → "Create trail"
   - Name + **S3 bucket** (events kahan jayenge)
   - "Apply to all regions" ON karo (multi-region)
2. **Event types select karo** — Management (default ON), Data events (ON karo for S3/Lambda)
3. **CloudWatch Logs** attach karo (alarms ke liye)
4. **Insight events** enable karo (default 90 days history)
5. Events JSON me S3 pe store hote hain:

```json
{
  "eventVersion": "1.08",
  "userIdentity": {
    "type": "IAMUser",
    "userName": "abdullah",
    "accessKeyId": "AKIA..."
  },
  "eventTime": "2026-08-15T14:32:10Z",
  "eventSource": "s3.amazonaws.com",
  "eventName": "DeleteBucket",
  "awsRegion": "us-east-1",
  "sourceIPAddress": "182.x.x.x",
  "userAgent": "console.amazonaws.com",
  "requestParameters": { "bucketName": "my-important-bucket" },
  "responseElements": null
}
```

**Search & queries:**
```bash
# CLI se events dhundna
aws cloudtrail lookup-events --lookup-attributes \
  AttributeKey=EventName,AttributeValue=DeleteBucket \
  --max-results 10

# S3 me stored events (Athena se query karo!) 
# CloudTrail → S3 → Athena table banao → SQL:
SELECT eventname, useridentity.username, eventtime, sourceipaddress
FROM cloudtrail_logs
WHERE eventname LIKE '%Delete%'
ORDER BY eventtime DESC;
```

**Important points:**
- **Free:** Management events — **90 days Event History** (console pe browse)
- **Cost:** S3 storage + CloudWatch Logs + Data events (per 100k events)
- **Default ON** — management events saare accounts me log hoti hain (90 days)
- **Durable storage** — S3 me trail set karo, warna 90 din baad history expire
- **Security best practice:** trail logs ko bhi protect karo (S3 bucket policies, MFA delete)
- **KMS encryption** — cloudtrail logs encrypt karna
- CloudTrail + CloudWatch + Athena = complete audit pipeline
- **Organization trail** — multiple accounts ek jagah audit

**Analogy:** CloudTrail = **Office CCTV + register book** — har kisi ka entry/exit (API call) recorded hota hai: kaun office aaya (IAM user), kab, kya kiya (eventName). Kuch bhi miss nahi hota — agar koi galti kare to pehle CCTV check karo. Security team kisi bhi suspicious activity ko usi register se investigate karti hai.

## Config (AWS Config)

Config AWS ki **resource configuration tracking & compliance** service hai — jo aapke AWS resources (EC2, S3, IAM, Security Groups, etc.) ki **configuration ko continuously record** karti hai, unke changes ko **history/timeline** me rakhti hai, aur **rules** se **compliance** check karti hai. Socho: "Mera environment ka document-holder, jo har badlav ko note karta hai aur rules ki khilaf warzi check karta hai."

**Example — "Security Group kisi ne khola to?"**
1. Ek developer EC2 security group me **port 22 (SSH) sab ke liye khol deta hai** (0.0.0.0/0)
2. **Config rule** set hai: "SG me port 22 sab ke liye nahi khulna chahiye"
3. Config detect karta hai → resource **NonCompliant** mark
4. Notification (SNS) → security team ko pata chal jata hai
5. Timeline me dekho — "kab khola, kaun khola, pehle kya tha" (config history)

**Config ke 3 main kaam:**

1. **Record** — har resource ka config snapshot + changes ki history (timeline)
2. **Evaluate** — rules se compliance check (custom ya managed rules)
3. **Remediate** — auto-fix non-compliant resources (SSM automation)

**Core concepts:**

| Concept | Kya hai |
|---------|---------|
| **Config recorder** | Resources ko continuously record karta hai (changes detect) |
| **Configuration item** | Resource ki state — snapshot (one point of time) |
| **Configuration history** | Saare changes — timeline (pehle kya tha → ab kya hai) |
| **Configuration snapshot** | Saare resources ka full dump (S3 me store) |
| **Resource timeline** | Ek resource ka config history (kab kya changed) |
| **Rules** | Compliance check — managed (AWS ready) ya custom (Lambda) |
| **Remediation** | Non-compliant resource → auto-fix |

**Managed Rules (AWS built-in) — examples:**

| Rule | Check karta hai |
|------|----------------|
| `restricted-ssh` | SG me port 22 public nahi |
| `s3-bucket-public-read-prohibited` | S3 bucket public nahi |
| `cloudtrail-enabled` | CloudTrail ON hai |
| `encrypted-volumes` | EBS encrypted |
| `iam-user-mfa-enabled` | Users pe MFA hai |
| `lambda-function-public-access-prohibited` | Lambda public nahi |
| `ec2-instance-no-public-ip` | EC2 me public IP nahi |
| `rds-storage-encrypted` | RDS encrypted |

**How it works:**

```
AWS Resources (EC2, S3, SG, IAM...)
     ↓ Config Recorder (har change detect)
Configuration Items → History + Snapshot (S3)
     ↓
Config Rules (managed/custom) → Compliance (Compliant/NonCompliant)
     ↓
SNS Notification → SQS → Alert/response (incident)
     ↓ (remediation)
SSM Automation → auto-fix
```

**Setup steps:**

1. **Config ON karo** — Console → Config → "Get started"
   - Choose: record all resources / specific regions
   - S3 bucket (snapshots) + SNS topic (notifications) select/banao
2. **Rules add karo:**
   - "Add rule" → Managed rules search karo (`restricted-ssh`, `s3-bucket-public-read-prohibited`)
   - Trigger: **On change** (resource badla) ya **periodic** (har 24h check)
3. **Compliance dashboard** — Config → Rules → Compliance (kya compliant, kya not)
4. **Remediation set karo** (optional) — "NonCompliant → SSM document run"
5. **Aggregator** (multi-account) — saare accounts ki compliance ek jagah

**Config vs CloudTrail:**

| Feature | AWS Config | CloudTrail |
|---------|-----------|------------|
| Track kya | **Resource configuration** (kya hai, kya tha) | **API actions** (kaun kya kiya) |
| Records | Config changes + history | API calls + events |
| Compliance | ✅ Rules/checks | ❌ (event log) |
| Timeline | ✅ Per-resource history | Events order me |
| Use case | "SG public hai" check, audit | "DeleteBucket kisne kia" investigation |
| Analogy | **Chef ka kitchen — saaman & recipe check** | **CCTV — kaun andar aaya** |

**Dono milakar (example):**
- CloudTrail: "Kisne khola port 22?" (call)
- Config: "Kya port 22 khula hai + kab khola gaya + konsa rule toota" (state + timeline)

**Important points:**
- **Pricing:** per rule per month + per config item (first 100k free)
- Config items: $0.003/item, Rules: $0.001/rule/month evaluation (approx)
- Exclude high-change resources (Lambda versioning) — cost bachao
- **Delivery channel** — S3 (history) + SNS (notifications) mandatory
- Aggregator → multi-account/region single view
- **Conformance packs** — templates (multi rules ek pack) — compliance frameworks
- Config + **SSM Automation** = auto-remediation (self-healing infra)

**Analogy:** AWS Config = **Quality inspector in factory** — machine (resource) ki har setting register karta hai, check karta hai "ye setting standard ke mutabik hai?" (rule). Agar machine galti se galat setting pe chali gayi (non-compliant) → inspector report karta hai + fix karvata hai (remediation). CloudTrail = **CCTV** jo baat ni karta ki machine galat hai, bas record karta hai "kisne button dabaya".

## X-Ray

X-Ray AWS ki **distributed tracing** service hai — jo ek request ko track karti hai jab wo **multiple services** se hoti hui jaati hai (API Gateway → Lambda → DynamoDB → SNS). Isse pata chalta hai: **request kahan atki, kaunsa service slow tha, error kahan aaya** — microservices architecture ki debugging ke liye.

**Example — "Mera order API 10 second me response kyun de raha hai?"**
Amazon ka order API 5 services se banta hai:
1. User request → **API Gateway** → **Lambda (validate)** → **DynamoDB (save)** → **SNS (notify)** → response
2. Request slow hai — kaunsa step?
3. **X-Ray** se trace dekho:
   - Lambda validate: 200ms ✅
   - **DynamoDB: 9.2 seconds** ❌ (slow query/slow done)
4. Turant pata chal gaya — DynamoDB slow query fix karo, baaki sab fast hai

**X-Ray core concepts:**

| Concept | Kya hai |
|---------|---------|
| **Trace** | Ek request ka pura journey (multiple segments) |
| **Segment** | Ek service ka part (jaise Lambda call) |
| **Subsegment** | Service ke andar ka call (DynamoDB, HTTP, DB query) |
| **Service map** | Visual graph — services + latency + errors |
| **Annotations** | Custom key-value (trace pe search karne ke liye) |
| **Sampling** | Har request ni — kuch % sample hoti hain (cost) |

**Trace kaise dikhta hai (simplified):**
```
Client → API Gateway (50ms) → Lambda validate (200ms) → DynamoDB (9.2s) ❌
                                        ↓
                                      SNS (30ms) — never reached? NO — slow pe
```
**Service Map** — visual:
```
API Gateway ──→ Lambda validate ──→ DynamoDB
     │                │
     │                └──→ SNS
     └── (User)
```
- Green/red circles — error/slow indicator
- Latency sab line pe dikhta hai

**Kaise instrument karta hai (SDK):**

| Language | SDK |
|----------|-----|
| Python | `aws-xray-sdk` |
| Node.js | `aws-xray-sdk-node` |
| Java / Go | AWS X-Ray SDK |
| Lambda | **Auto-instrumented** (handler ke andar) — SDK optional |
| API Gateway | Auto — trace headers pass |

**Setup steps:**

1. **X-Ray enable karo** (per service):
   - **Lambda:** X-Ray tracing ON (console/CLI) — `TracingConfig: Active`
   - **EC2/ECS:** X-Ray daemon install karo + SDK (agent)
2. **IAM permissions** — `AWSXRayDaemonWriteAccess` role/daemon pe
3. **SDK add karo** — app code me (capture call, annotation)
   ```python
   # Python example
   from aws_xray_sdk.core import xray_recorder
   xray_recorder.begin_subsegment("dynamodb_query")
   response = table.query(...)
   xray_recorder.end_subsegment()
   ```
   Ya Lambda/API Gateway ka **native integration** (auto)
4. **Console pe dekho** — X-Ray → Service map / Traces / Analytics
5. **Trace search** — annotations se filter ("order_id = 12345" ke liye)

**X-Ray vs CloudWatch:**

| Feature | X-Ray | CloudWatch |
|---------|-------|-----------|
| Type | **Distributed tracing** | Metrics & logs monitoring |
| Track | Request journey (service-to-service) | Resource health (metrics) |
| Sees | Latency/error **per call chain** | CPU, memory aggregate |
| Best for | Microservices — "request kahan atki" | Infrastructure — "server healthy?" |
| Visual | Service map/graph | Dashboards |

**Use cases:**
- **Slow requests debug** — kaunsa service bottleneck hai
- **Error tracing** — Lambda me error kahan hua
- **Dependency analysis** — koi service dusri pe kitna depend hai
- **Performance baselines** — latency tracking over time
- **Cost attribution** — trace pe annotations se (order_id, customer tier)

**Important points:**
- **Sampling ON** — 100% requests trace nahi hoti (default ~1%) — cost control
- **Cost:** per trace ($0.0000005/trace ≈ 1M ~$0.50 — approx) + storage
- **Segment limits:** 500 segments/trace — deep chains bhi covered
- **Lambda native** — bina SDK ke bhi basic traces
- X-Ray + CloudWatch = **complete observability** (metrics + logs + traces)
- **OpenTelemetry** — X-Ray OTLP endpoint support (modern SDK option)

**Analogy:** X-Ray = **Tracker app for your request** — delivery (request) ka live status: "kahan hai, kitna aage badha, kis stop pe atka". Service map = **Delivery route map** — saare stops (services) + har stop ka time. CloudWatch = **Weather radar** — infrastructure health watch karta hai, but request ka detailed journey X-Ray ka kaam hai.

## CodeBuild (CI/CD)

CodeBuild AWS ki **fully managed build service** hai — jo aapke source code ko **compile, test aur package** karti hai. Jad bhi developer code push kare, CodeBuild **build environment** me us code ko binary/deployable artifact bana deta hai. **CI/CD ka "C" (Continuous Integration)** — manual build servers nahi lagane padte.

**Example — Amazon ka mobile app CI:**
Amazon ka app developers push karte hain:
1. Code repository (GitHub/CodeCommit) me push → **webhook** trigger
2. **CodeBuild** source pull karta hai → **build environment** (container) me
3. Build steps: dependencies install → `npm test` / `run tests` → `build` → **artifact** (zip)
4. Artifact **S3** me stored — aur **CodePipeline** ka next step (CodeDeploy) ko pass
5. Failed build → **notification** (SNS) — developer ko email — "tumhara build fail"
6. Success → continue pipeline (deploy)

**CI/CD kya hota hai?**

```
CI (Continuous Integration) → Continuous Delivery/Deployment (CD)
Code push (developer) → Build (CodeBuild) → Test → Artifact → Deploy (CodeDeploy/Lambda/S3)
```

| Step | Service | Kya hota hai |
|------|---------|--------------|
| 1. Source | **CodeCommit** / GitHub / S3 / Bitbucket | Code repository |
| 2. Pipeline | **CodePipeline** | Steps orchestrate (source → build → deploy) |
| 3. Build | **CodeBuild** | Compile + test + package (artifact) |
| 4. Deploy | **CodeDeploy** / ECS / Lambda / S3 | Artifact ko production pe push |

**CodeBuild key features:**
- **Fully managed** — koi servers, autoscale build, pay per build (build-minutes)
- **Build environments** — ready-made images: Ubuntu, Amazon Linux, Node.js, Python, Java, Go, .NET — ya custom Docker image
- **Buildspec file** (`buildspec.yml`) — build commands definition (written in repo)
- **Artifacts** — build output S3 pe store
- **VPC support** — private resources access (RDS, ALB tests)
- **CloudWatch Logs** — build logs
- **Batch builds** — multiple build definitions
- **Caching** — dependencies cache (S3) — fast rebuilds

**buildspec.yml example:**
```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 20
    commands:
      - npm install
  pre_build:
    commands:
      - npm test
  build:
    commands:
      - npm run build
  post_build:
    commands:
      - echo "Build complete!"
artifacts:
  files:
    - '**/*'
  base-directory: dist
```

**Setup steps:**
1. **Build project banao** — Console → CodeBuild → "Create build project"
   - Source: GitHub (connect) / CodeCommit / S3
   - Environment: OS + runtime (Ubuntu + Node 20) — image
   - Service role (IAM) — S3 artifact + logs access
   - Buildspec: **use repository's buildspec.yml** (ya inline)
2. **Build run karo** — "Start build" (manual) ya pipeline/webhook auto
3. **Build logs** — CloudWatch pe live logs
4. **Artifacts check karo** — S3 artifact bucket

**CLI example:**
```bash
# Build start karo
aws codebuild start-build --project-name my-app-build

# Build ka status
aws codebuild batch-get-builds --ids "my-app-build:xxxx"
```

**CodeBuild vs Jenkins (self-hosted):**

| Feature | CodeBuild | Jenkins |
|---------|----------|---------|
| Managed | ✅ Serverless | ❌ Server install/manage karo |
| Scale | Auto | Manual agents |
| Cost | Pay per build-minute | Server + agents 24x7 |
| Setup | Minutes | Hours (plugins, config) |
| Flexibility | Less (fixed structure) | Extreme (plugins, groovy) |
| When | AWS-native, simple pipelines | Complex build processes, on-prem |

**CodePipeline (related):**
- CI/CD **orchestrator** — stages: Source → Build → Test → Deploy
- CodeBuild pipeline ke **build stage** me chalti hai
- CodePipeline + CodeBuild + CodeDeploy = full CI/CD

**Pricing:**
- **Free tier:** 100 build-minutes/month (Linux, small instance) — 1 year
- After: ~$0.005/min (general) — Linux/Windows alag
- **Savings:** Reserved capacity discounts (large teams)

**Important points:**
- **buildspec.yml** repository me — pipeline reproducible
- **Tests chalao** — build me tests include karo, warna broken code deploy ho jayega
- Artifacts S3 me — **versioning** bucket use karo
- **Environment variables** — secrets abhi CodeBuild me — **Secrets Manager/SSM** reference karo
- **Cache** — dependencies cache se build time 50%+ kam
- Private code = **CodeCommit** (GitHub Actions alternate: GitHub-native)

**Analogy:** CodeBuild = **Automatic kitchen (kitchen robot)** — aap recipe (source code) dete ho, kitchen (build environment) ready hai, robot steps follow karta hai (install, test, build) aur packaged dish (artifact) nikalta hai. Aapko plates (servers) nahi kharidni padti — robot rental hai. Jenkins = **Bande ka kitchen** — sirf tumhare ghar (server) me, tum khud sambhalo.

## CodeDeploy

CodeDeploy AWS ki **fully managed deployment** service hai — jo aapki app (built artifact) ko **EC2, Lambda, ECS, ya on-premises servers** pe **automatically deploy** karti hai — bina downtime, bina manual steps. CI/CD ka "D" — **Continuous Delivery**. Deployment strategy (blue/green, rolling) khud choose karo.

**Example — Amazon ka mobile API deploy:**
Amazon ka API 10 EC2 instances pe chalta hai:
1. Developer code push → **CodePipeline** build (CodeBuild) → artifact ready
2. **CodeDeploy** deploy application: 10 instances pe app version v2.1
3. **Rolling deploy** — ek-ek karke: instance 1 update → health check ✅ → next
4. Agar version fail hota (health check fail) → **auto-rollback** to v2.0
5. Users ko **zero downtime** — kabhi server down nahi dikhi

**CodeDeploy core concepts:**

| Concept | Kya hai |
|---------|---------|
| **Application** | Aapki app ka name (deployment unit) |
| **Deployment group** | Instances/targets ka group (tags, ASG, Lambda aliases) |
| **Revision** | App ka version — artifact + appspec (S3 ya GitHub) |
| **AppSpec file** (`appspec.yml`) | Deployment instructions (kaise install, start, verify) |
| **Deployment** | Revision ko group pe push karne ka process |
| **Hook** | Lifecycle event — before/after install me script chalao |

**Targets (kahan deploy karta hai):**

| Target | Use Case |
|--------|----------|
| **EC2 / on-premises** | Web servers, app servers (rolling/blue-green) |
| **Lambda** | Serverless function version deploy (canary/linear) |
| **ECS** | Containers (ECS service update) |
| **ASG (Auto Scaling Group)** | Instances group pe rolling deploy |

**appspec.yml example (EC2):**
```yaml
version: 0.0
os: linux
files:
  - source: /app/
    destination: /var/www/myapp
hooks:
  AfterInstall:
    - location: scripts/restart_server.sh
      timeout: 300
  ApplicationStart:
    - location: scripts/start_app.sh
  ValidateService:
    - location: scripts/health_check.sh
      timeout: 60
```

**Deployment strategies:**

| Strategy | Kya hota hai | Use Case |
|----------|--------------|----------|
| **In-place (rolling)** | Instances ek-ek karke update (same instances) | Small apps, quick |
| **Blue/Green (EC2/ECS)** | Naye batch (green) pe deploy → traffic switch → old (blue) terminate | Zero downtime, easy rollback |
| **Canary (Lambda)** | 10% traffic naye version pe → baaki 90% (linear) | Serverless gradual rollout |
| **Linear** | Fixed % har 10 min (jaise 10% → 100%) | Controlled rollout |
| **All-at-once** | Sab instances ek saath | Testing, non-prod |

**Blue/Green kaise kaam karta hai:**
```
Blue (v2.0 — production, 10 instances)
  ↓ DockerImage/EC2 me Green launch karo
Green (v2.1 — nayi, 10 instances ready)
  ↓ tests pass
Traffic switch → Green
  ↓ (rollback = traffic wapas Blue)
Blue terminate (ya keep hold)
```
- **Zero downtime** — Green ready hone ke baad hi traffic switch
- **Rollback easy** — bas traffic wapas Blue pe
- Cost: extra instances (Green) — deploy time ke liye

**Lambda deployment (Canary example):**
```
v2 warm-up → 10% traffic v2 → observe → 90% → v2.1 live
Fail? → rollback to v1 (traffic)
```

**Setup steps:**

1. **Appspec + artifact ready karo** — `appspec.yml` repo me hai, CodeBuild ya manual se artifact S3 pe
2. **Application banao** — Console → CodeDeploy → "Create application" → Compute platform: EC2/Lambda/ECS
3. **Deployment group banao:**
   - EC2: tags se instances select karo (jaise `Environment:Production`)
   - ya ASG name, role (EC2 pe install agent + S3 access)
4. **Deployment trigger karo:**
   - Manual: "Create deployment" → artifact S3 path → strategy
   - **CodePipeline** se auto — pipeline ke Deploy stage me CodeDeploy action
5. **Monitor** — Deployment details (instances, hooks), **CloudWatch** events, logs
6. **Rollback** — automatic (fail pe) ya manual

**CodeDeploy vs ECS/EKS update vs EC2 manual:**

| Feature | CodeDeploy | Manual (SSH/Ansible) |
|---------|-----------|----------------------|
| Automation | ✅ Full | Scripts likhne padte |
| Rollback | ✅ Auto/manual | Manual (pehle backup) |
| Health checks | ✅ ValidateService hook | ❌ |
| Blue/Green | ✅ Built-in | Complex scripted |
| AWS-native | ✅ (ASG, Lambda, ECS) | ❌ |

**Pricing:**
- **Free** — CodeDeploy koi extra cost nahi (EC2/on-prem/Lambda/ECS) — sirf EC2/Lambda/ECS infra cost
- EC2 pe CodeDeploy agent ke liye IAM role chahiye

**Important points:**
- **appspec.yml** = deployment ka map — lifecycle hooks me scripts (install, start, health-check)
- **Health checks mandatory** — ValidateService hook — warna broken deploy bhi pass
- **Rollback config** — deployment fail → auto rollback wali revision pe
- **Trigger** (CloudWatch alarms/events) — deployment fail ya success pe SNS
- CodePipeline (CI) → CodeBuild (build) → **CodeDeploy (deploy)** = full CD pipeline
- ECS CodeDeploy = **blue/green container deploy** — zero downtime ECS update

**Analogy:** CodeDeploy = **Airplane cabin crew upgrade check** — plane (server) khud nahi badalti, lekin crew (app) ka version aur process oversees. Blue/Green = **Weather-based off-summer hotel switch** — naya hotel (Green) pehle ready, guests (traffic) shift, phir purana (Blue) band. Rolling = **Traffic signal ek-ek kar ke** — kabhi full road band nahi.

## CodePipeline

CodePipeline AWS ki **CI/CD orchestration service** — jo aapki **release pipeline** ko end-to-end manage karti hai: **Source → Build → Test → Deploy** — har stage ek door hai, har successful stage ke baad agla stage chalta hai. Ye **CodeCommit, GitHub, CodeBuild, CodeDeploy, Lambda, ECS, S3, CloudFormation** sab ko ek saath **aurchestrate** karta hai.

**Example — Amazon ka app release pipeline:**
1. Developer code push karta hai GitHub pe
2. **Pipeline detect** karta hai (webhook) → Source stage (GitHub pull)
3. **Build stage** — CodeBuild: test + build → artifact S3
4. **Test stage** — CodeDeploy/ALB pe deploy → integration tests
5. **Approval stage** — Manager ko email (manual approval) → Approve
6. **Deploy stage** — CodeDeploy production pe blue/green deploy
7. Fail hua → pipeline stops + notification, rollback config

**Pipeline concepts:**

| Concept | Kya hai |
|---------|---------|
| **Pipeline** | End-to-end release workflow (stages ka sequence) |
| **Stage** | Logical step (Source, Build, Test, Deploy, Approval) |
| **Action** | Stage ke andar ka task (CodeBuild run, S3 copy) |
| **Artifact** | Stage ka output (source zip, build output) — S3 me stored |
| **Transition** | Stage complete hone par agle pe move |
| **Manual approval** | Stage — insaan approve kare tab aage jaye |
| **Webhook/Event** | Source change (push) → pipeline auto-trigger |

**Pipeline architecture (typical):**
```
Source          → Build      → Test        → Approval      → Deploy
(GitHub/CodeCommit)  (CodeBuild)   (CodeDeploy)   (Manual: email)   (CodeDeploy/Lambda)
     ↓                ↓              ↓                ↓                 ↓
  Artifact:zip    Artifact:build   Integration test   manager approve   Production
```

**Actions/stages (real-world pipeline):**

| Stage | Action (service) | Purpose |
|-------|------------------|---------|
| Source | GitHub / CodeCommit / S3 | Latest code pull |
| Build | CodeBuild | Compile, test, package |
| Deploy (QA) | CodeDeploy / ECS | Test environment |
| Test | CodeBuild (integration tests) / Lambda | E2E tests |
| Approval | Manual (SNS email) | Human gate |
| Deploy (Prod) | CodeDeploy (blue/green) / CloudFormation / Lambda | Production release |

**Setup steps:**

1. **Pipeline banao** — Console → CodePipeline → "Create pipeline"
   - **Source stage:** GitHub (connect) / CodeCommit repo → branch
   - **Build stage:** CodeBuild project (ya create nayi)
   - **Deploy stage:** CodeDeploy application / Lambda / ECS / S3 / CloudFormation
2. **Artifact bucket** — CodePipeline apne S3 bucket me artifacts store karta hai (auto-creates)
3. **Manual approval add karo:**
   - Add stage → "Manual approval" action → SNS topic (email)
   - Reviewer approve → next stage
4. **Webhook/Event trigger** — GitHub push → pipeline auto-run (source change detection ON)
5. **Run + monitor** — Console pe pipeline visual: green check ✅ / red ❌ per stage
6. **Rollback** — deploy failed → CodeDeploy rollback; ya pipeline re-run (revision)

**CLI examples:**
```bash
# Pipeline list
aws codepipeline list-pipelines

# Pipeline start (manual)
aws codepipeline start-pipeline-execution --name my-app-pipeline
```

**CodePipeline vs Jenkins:**
- CodePipeline = **AWS-native orchestration** (Code* services ek saath)
- Jenkins via **CodePipeline Jenkins plugin** — hybrid (on-prem agents)
- Jenkins = full custom (groovy/pipelines), halki learning curve; CodePipeline = visual, serverless

**Pricing:**
- **Free:** 1 pipeline free (30 days) — after: **$1 per active pipeline per month**
- Extra: action executions (after 1 per month free) — ~$0.002 per action execution

**Important points:**
- **Active pipeline** = definition hi cost deti hai (runs free for first pipeline)
- **Artifacts S3** — encryption enable karo (KMS)
- **Retry** — failed stage pe "Retry" button (same action) 
- **Cross-account/region** — pipelines multiple regions/accounts me deploy
- **Notifications** — CloudWatch events: pipeline notified (SNS/Slack)
- **Best practice:** Test + Approval stage production ke pehle
- ECS deploy action = blue/green — ECS CodeDeploy integration
- **Pipeline execution history** — 30 days (max 1000)

**Analogy:** CodePipeline = **Assembly line conveyor** — har station (stage) ek kaam karta hai: station 1 code greht hai, station 2 build, station 3 test, wo bhi approval gate (insaan) khulti hai, aakhri station deploy. Ek station fail → poori line stop + alarm. Line khud chalti hai jab naya code (product) aata hai — aapko koi step manually nahi karna.

## CloudFormation (Infrastructure as Code)

CloudFormation AWS ki **Infrastructure as Code (IaC)** service hai — jisme aap **YAML/JSON template** me apni poori infrastructure (EC2, S3, VPC, IAM, RDS...) define karte ho, aur CloudFormation usse **stacks** me banata/update/detele-karta hai. **Code jaise infra treat karo** — version control, review, reuse, automation.

**Example — Amazon ka web app infra setup:**
1. Developer template likhta hai: 2 EC2 (t3.micro), 1 ALB, 1 RDS, security groups, IAM role
2. `aws cloudformation deploy` chalao (ya console)
3. CloudFormation **saare resources order me** banata hai (dependencies resolve)
4. **Rollback** — beech me EC2 fail → saare created resources cleanup + error
5. 2nd environment (staging/prod) → same template → seedha deploy

**Kyun IaC (bina manual console)?**

| Aspect | Manual (Console/CLI) | CloudFormation |
|--------|---------------------|----------------|
| Setup time | Hours — clicking | Minutes — template deploy |
| Repeatability | Har baar naya setup | **Same result har baar** |
| Version control | ❌ | ✅ (template git me) |
| Review | ❌ | ✅ (code review) |
| Consistency | Human error possible | Deterministic |
| Rollback | Manual | ✅ Auto |

**Core concepts:**

| Concept | Kya hai |
|---------|---------|
| **Template** | Infra ki definition (YAML/JSON) — "recipe" |
| **Stack** | Template se banne wale resources ka group (live infra) |
| **Resource** | Ek AWS service instance (bucket, instance, role) |
| **Parameters** | Input values (env, instance size, names) — deploy time pe |
| **Outputs** | Stack ke values (EC2 IP, bucket name) — dusre stacks me use karo |
| **Mappings** | Env-specific values lookup (prod → m5.large) |
| **Conditions** | If-then create resources (Prod me multi-AZ, Dev me nahi) |
| **Change Set** | Preview — deploy se pehle "kya badlega" (dry-run) |
| **Drift detection** | Manual changes ka pata lagana (template vs reality) |
| **Nested stacks** | Stack ke andar stack (reuse modules) |

**Template example (EC2 + S3 + security group):**
```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: Simple web server stack

Parameters:
  InstanceType:
    Type: String
    Default: t3.micro
    AllowedValues: [t3.micro, t2.micro, m5.large]

Resources:
  WebServerSG:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow HTTP
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

  WebServer:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: ami-0abcdef1234567890
      SecurityGroupIds:
        - !Ref WebServerSG
      UserData:
        Fn::Base64: |
          #!/bin/bash
          yum install -y httpd
          systemctl start httpd

  WebsiteBucket:
    Type: AWS::S3::Bucket
    DeletionPolicy: Retain   # delete pe bucket nahi jayega

Outputs:
  ServerIP:
    Value: !GetAtt WebServer.PublicIp
```

**Intrinsic functions (template me helper):**
- `!Ref` — resource/parameter ka reference (name/ID)
- `!GetAtt` — resource ki property (PublicIp, ARN)
- `!Sub` — string interpolation (`${EnvName}-bucket`)
- `!Join / !Split` — strings combine/split
- `!If` — condition logic
- `!Select / !FindInMap` — list/map se value

**CLI commands:**
```bash
# Stack deploy karo (template + parameters)
aws cloudformation deploy \
  --template-file template.yml \
  --stack-name my-app-stack \
  --parameter-overrides Env=prod InstanceType=m5.large \
  --capabilities CAPABILITY_IAM

# Status check
aws cloudformation describe-stacks --stack-name my-app-stack
aws cloudformation list-stack-resources --stack-name my-app-stack

# Changes preview (deploy se pehle)
aws cloudformation create-change-set \
  --stack-name my-app-stack \
  --template-body file://template.yml \
  --change-set-name my-changes

# Stack delete (saare resources cleanup)
aws cloudformation delete-stack --stack-name my-app-stack
```

**Setup steps (console):**
1. Template file repo me rakho (YAML) — git versioned
2. Console → CloudFormation → "Create stack" → template upload (ya S3)
3. **Parameters** fill karo (stack name, env, instance type)
4. Options: IAM capabilities, tags, rollback settings
5. **Review + create** — CloudFormation resources banata hai (Events tab pe live)
6. **Update** — template edit karo → deploy → Change Set review → execute
7. **Delete** — poora stack cleanup (saare resources galat ho jate hain)

**Drift detection:**
- Agar koi console se manually resource badal de (template se bahar)
- "Detect drift" → stack status: **DRIFTED** (resource alag)
- Fix: template ke mutabik wapas lao ya update

**CloudFormation vs Terraform:**

| Feature | CloudFormation | Terraform |
|---------|---------------|-----------|
| Vendor | AWS-only | **Multi-cloud** (AWS, GCP, Azure) |
| Language | YAML/JSON (native) | **HCL** (Terraform language) |
| State tracking | AWS handles (free) | State file (S3/backend) |
| Modules/Reuse | Nested stacks | Modules (registry) |
| AWS integration | ✅ Deepest (custom resources, CDK) | Good (providers) |
| When | AWS-only infra | Multi-cloud, team already terraform |

**CloudFormation vs CDK:**
- **CDK (Cloud Development Kit)** — same CloudFormation, but **code languages** (TypeScript/Python/Java) me template define
- CloudFormation = YAML template; CDK = code → compiles to CloudFormation template
- CDK = better for developers (logic, loops)

**Pricing:**
- **Free** — CloudFormation service koi cost nahi
- Sirf resources ki cost (EC2, RDS jo template banata hai)

**Important points:**
- **Rollback on failure** — default ON — failed stack auto-cleanup
- **UpdateRollback** — update fail → pehle version restore
- **DeletionPolicy** — `Retain` (bucket delete pe bhi rehta) / `Snapshot` (EBS backup)
- **Stack sets** — ek template multiple accounts/regions pe
- **Parameter store** — secret values CloudFormation me directly mat dalo — SSM/Secrets Manager reference karo
- **Nested stacks** — modular infra (network stack, app stack)
- **Change Sets** hamesha review karo — production me accidental delete se bacho

**Analogy:** CloudFormation = **Blueprint for building house** — architect (developer) ek drawing (template) banata hai, contractor (CloudFormation) uske mutabik poora house (stack) banata hai — har kamra (resource) sahi jagah, sahi order me. Agar koi kamra galat bana (fail) → poora kaam wahi ruk jaata hai (rollback). Naya makaan (staging) — wahi blueprint — 1 minute. Terraform = dusra contractor jo kisi bhi city me ghar bana sakta hai (multi-cloud), Blueprint yahi rahega.

## CDK (Cloud Development Kit)

CDK AWS ka **Infrastructure as Code (IaC) framework** — jisme aap infra ko **programming languages** me likhte ho (TypeScript, Python, Java, Go, C#, JavaScript) — YAML khud nahi likhna padta. CDK code ko compile karke **CloudFormation template** banata hai — phir wahi CloudFormation infra deploy karta hai. **Infra = code — real programming power.**

**Example — Amazon ka Lambda + API Gateway infra (Python):**
```python
from aws_cdk import (
    aws_lambda as lambda_,
    aws_apigateway as apigateway,
    Stack, App, Duration,
)

class MyApiStack(Stack):
    def __init__(self, scope, id, **kwargs):
        super().__init__(scope, id, **kwargs)

        # 1. Lambda function
        fn = lambda_.Function(
            self, "MyHandler",
            runtime=lambda_.Runtime.PYTHON_3_12,
            handler="handler.main",
            code=lambda_.Code.from_asset("lambda/"),
            timeout=Duration.seconds(30),
        )

        # 2. API Gateway — Lambda se connected
        apigateway.LambdaRestApi(
            self, "MyApi",
            handler=fn,
            proxy=False,
        )

# Deploy:
app = App()
MyApiStack(app, "MyApiStack")
app.synth()   # → CloudFormation template generate
```

**CDK kaise kaam karta hai?**

```
CDK code (Python/TS) → `cdk synth` → CloudFormation template (auto-generated)
                                        ↓
                            `cdk deploy` → Stacks live (AWS)
```

- **Constructs** — reusable building blocks (resources ya component groups)
- **L3 Constructs** — high-level: ek line me poora pattern (ALB + ASG, S3 + CloudFront)
- **L1 Constructs** — CloudFormation resources ka 1:1 mapping (`CfnBucket`)
- **CDK best practice** — **L3 constructs** use karo (patterns, sane defaults)

**CDK vs CloudFormation vs Terraform:**

| Feature | CDK | CloudFormation | Terraform |
|---------|-----|----------------|-----------|
| Language | **Python/TS/Java/Go/C#** | YAML/JSON | HCL |
| Programming power | ✅ loops, if, classes, functions | ❌ (template only) | ❌ (limitada) |
| Logic reuse | ✅ NPM/Maven/PyPI packages | Nested stacks | Modules |
| AWS support | ✅ Deepest quickly | ✅ | Good |
| Multi-cloud | ❌ | ❌ | ✅ |
| Deployment engine | CloudFormation | itself | itself |
| Best for | **Developers**, complex logic infra | Simple/team YAML-dosti | Multi-cloud teams |

**CDK key features:**
- **Real programming** — loops se 10 buckets, if-condition se resources, functions se reuse
- **Type safety** — errors compile time pe pata chalte hain (YAML me runtime pe)
- **Construct library** — 5000+ pre-built components (registry)
- **`cdk watch`** — file change pe auto-deploy (dev loop)
- **`cdk destroy`** — poora stack cleanup
- **Diff command** — `cdk diff` — deploy se pehle kya badlega
- **App/Stack/Stage** — multi-stack, multi-env (dev/prod) ek app me
- **Context** — env-specific values (parameter store jaise)

**Setup steps:**

1. **CDK install karo:**
   ```bash
   npm install -g aws-cdk
   cdk --version
   ```
2. **App init karo:**
   ```bash
   mkdir my-infra && cd my-infra
   cdk init app --language python
   # (Ya: --language typescript/java/go/csharp)
   ```
3. **Code likho** — `app.py` (ya main.ts) me constructs
4. **`cdk synth`** — CloudFormation template generate karo (check)
   ```bash
   cdk synth   # template.json banta hai
   ```
5. **`cdk deploy`** — stack deploy
   ```bash
   cdk deploy MyApiStack
   ```
6. **`cdk diff` / `cdk destroy`** — update preview / cleanup

**CDK commands:**
```bash
cdk list          # stacks list
cdk synth         # template generate (kuch nahi deploy)
cdk diff          # changes preview (deploy se pehle)
cdk deploy        # deploy stack
cdk deploy --all  # saare stacks
cdk destroy       # stack delete
cdk watch         # auto-deploy on change (dev)
```

**Important points:**
- **CDK internally CloudFormation** use karta hai — template auto-generated — so CloudFormation features (rollback, change sets) sab available
- **Bootstrap needed** — pehli baar `cdk bootstrap` (S3 bucket for assets) — deploy se pehle 1 baar
- **L3 constructs** — sane defaults, less code (Best practice)
- **Package ecosystem** — publish own constructs (npm/PyPI) — team me share
- **CDK + CodePipeline** — infra CI/CD: code push → cdk deploy (cross-account)
- **Snippets/types** — IDE support (VS Code: AWS Toolkit)
- **Versioning** — CDK version + CloudFormation template lock
- **Cost** — CDK free (CloudFormation free) — sirf resources ka bill

**Analogy:** CloudFormation = **Architect's hand-drawn blueprint (YAML)** — sahi hai but har baar haath se drawing. CDK = **Architecture software (AutoCAD-like)** — aap code/buttons (programming) se design karte ho, wo khud blueprint (CloudFormation) print kar deta hai. Loops = ek click me 10 floors (buckets) banjana. Developer ke liye — code likhna aata hai, YAML nahi — **CDK = infra in Python/TS.**

## Amplify (Developer Productivity)

Amplify AWS ka **full-stack development platform** — jo developers ko **web/mobile apps** poori tarah banane mein madad karta hai: **backend (auth, data, storage, functions) + hosting (frontend deploy) + CI/CD** — sab ek jagah, minimum setup. AWS backend ki complexity ko simple SDK/CLI me wrap kar deta hai — developer sirf app logic pe focus karta hai.

**Example — Amazon ka customer app (React):**
1. Developer VS Code me React app likhta hai
2. `amplify add auth` → login/signup (Cognito behind the scenes)
3. `amplify add api` → GraphQL API + DynamoDB table (AppSync backend)
4. `amplify add storage` → S3 for user uploads
5. `amplify push` → sab AWS me banta hai (CloudFormation se)
6. `amplify publish` → app **hosted** on Amplify Hosting (CDN + SSL + custom domain)
7. Code push GitHub pe → **CI/CD auto**: build + deploy — preview PRs

**Amplify kitne cheezein karta hai (2 parts):**

| Part | Kya hai |
|------|---------|
| **Amplify Gen 2 (backend as code)** | Backend code me define karo (TypeScript) — auth, data, functions — CDK powered |
| **Amplify Hosting** | Web app hosting — static + SSR (Next.js) + global CDN + SSL + CI/CD |

**Amplify backend services (behind the scenes):**

| Amplify Component | AWS service (actually) |
|-------------------|------------------------|
| `amplify add auth` | **Cognito** (User Pools — sign up/in) |
| `amplify add api` | **AppSync** (GraphQL) + DynamoDB |
| `amplify add storage` | **S3** |
| `amplify add function` | **Lambda** |
| `amplify hosting` (deploy) | CloudFront (CDN) + S3, SSR |
| Backend as Code (Gen 2) | **CDK/CloudFormation** (compiled) |

**Key features:**
- **Backend as code (Gen 2)** — TypeScript me define, `amplify deploy`
- **Auth UI ready** — login/register widgets (Cognito backed)
- **GraphQL/Data** — AppSync — filters, real-time subscriptions
- **Hosting w/ CI/CD** — GitHub/GitLab/Bitbucket connect → auto deploy + **PR previews**
- **SSR support** — Next.js, Remix, Astro (server-side rendering)
- **Multi-environment** — dev/staging/prod (enabled via branches)
- **SDKs** — React, Vue, React Native, Swift, Android, Flutter — ready helpers
- **Local dev** — `amplify sandbox` — local mock (auth/data)

**Workflow (Gen 2):**
```
amplify init           # project setup
amplify add auth       # define auth in backend/auth/resource.ts
amplify add api        # define data schema (GraphQL)
amplify add storage    # S3 bucket
amplify deploy         # backend AWS me banao (CDK)
amplify publish        # frontend host karo
```
CI/CD: GitHub push → branch maag → built+deployed — PR preview link milega

**Example — auth config (resource.ts):**
```ts
import { defineAuth } from '@aws-amplify/backend';

export const auth = defineAuth({
  loginWith: {
    email: true,
  },
  userAttributes: {
    'custom:role': { required: false },
  },
});
```

**Amplify vs EC2/ECS hosting (traditional):**

| Feature | Amplify | EC2/ECS (manual) |
|---------|---------|------------------|
| Setup | **Minutes** (CLI/console) | Hours/days (VPC, SG, deploy config) |
| Hosting | Managed (CDN+SSL) | Build khud setup karo |
| CI/CD | ✅ Built-in (branch deploy) | CodePipeline manually jodo |
| Backend | ✅ Included (auth/data/storage) | Khud IAM + services |
| Scaling | Auto (CloudFront + serverless) | Auto Scaling config karna |
| Best for | **Frontend-heavy apps (React/Next.js), MVPs, demos** | Custom server apps, complex infra |

**Pricing:**
- **Amplify Hosting:** pay as you go — build minutes + hosting (CDN data transfer) — starting free tier (~1000 build min/mo? apna account check karo)
- Backend services — **Cognito/S3/AppSync/Lambda** ka normal cost (serverless, low cost for dev)
- Ping: MVP ke liye ~free tier ke andar enough

**Important points:**
- **Gen 2 = TypeScript (backend as code)** — power of CDK bina YAML headaches
- **`amplify sandbox`** — local dev environment (fast iteration)
- **PR previews** — GitHub PR pe live preview link — review ke liye best
- **Custom domains** — route53/custom domain attach (easy setup)
- **Amplify Admin UI (Studio)** — non-devs bhi data/auth manage kare
- **Limits:** Hosting SSR regions — check (us-east-1, eu-west-1...) — free tier compute limits
- Best for: **frontend teams** — backend dev ki complexity na chahte hon

**Analogy:** Amplify = **IKEA furniture for full app** — aapko har cheez alag alag nahi khareedni (Cognito, AppSync, S3, CloudFront — alag alag stores). Amplify = **One kit** — login (auth), data (database), storage, hosting sab box me aata hai — aap instructions (CLI/TS config) follow karte ho, sab ek saath ghar pe ready. Hosting = IKEA pickup wala delivery — ek click me deploy.

## AppSync (GraphQL API)

AppSync AWS ki **managed GraphQL API** service hai — jo apps ko ek **flexible, type-safe API** deti hai: client **exactly wo data** mangta hai jo chahiye (over-fetching nahi), aur **real-time subscriptions** (live updates) built-in hain. REST API ki jagah — ek hi endpoint se saare queries/mutations/subscriptions. **Amplify ka `add api` backend ke peeche AppSync hi hota hai.**

**Example — Amazon shopping app:**
Amazon ka app: product list, cart, orders, live stock updates chahiye:
1. **GraphQL schema** define karo: `Product`, `Order`, `Cart` types + queries (products, orders) + mutations (addToCart, placeOrder)
2. **Resolver** har field ko DynamoDB/Lambda se connect karta hai
3. App **one query** me product + stock + reviews le sakta hai (REST me 3 API calls)
4. **Subscription** — kisi aur ne order kiya → cart/stock **live update** (real-time)
5. **Offline sync** — app internet na ho to bhi kaam karta hai, wapas aane par sync

**GraphQL vs REST — basic difference:**

| Feature | REST | GraphQL (AppSync) |
|---------|------|-------------------|
| Endpoints | Multiple (/products, /orders) | **Ek hi endpoint** (POST /graphql) |
| Data fetching | Over/under-fetch (fixed response) | **Exactly jo maango** |
| Versioning | /v1, /v2 endpoints | Schema evolution |
| Real-time | WebSocket alag se | **Subscriptions built-in** |
| Client cache | Manual | **Normalized cache** (Apollo) |

**AppSync core components:**

| Concept | Kya hai |
|---------|---------|
| **Schema** | Types + Queries/Mutations/Subscriptions ka definition (GraphQL SDL) |
| **Resolver** | Field ka data kahan se aayega (DynamoDB, Lambda, HTTP, RDS, OpenSearch) |
| **Data sources** | DynamoDB, Lambda, RDS, HTTP, OpenSearch, Aurora |
| **Subscription** | Live updates — client subscribe karta hai, data change pe push |
| **Auth modes** | API key, Cognito, IAM, OIDC, Lambda authorizer |
| **Offline/Conflict resolution** | Offline mutations queue → sync → conflict resolve (last-writer-wins ya Lambda) |
| **AppSync APIs vs GraphQL endpoints** | Managed service vs khud GraphQL server |

**Schema example:**
```graphql
type Product {
  id: ID!
  name: String!
  price: Float!
  stock: Int!
}

type Query {
  products: [Product!]!
  product(id: ID!): Product
}

type Mutation {
  updateStock(id: ID!, stock: Int!): Product!
}

type Subscription {
  onStockChange(id: ID!): Product
    @aws_subscribe(mutationFields: ["updateStock"])
}
```

**Resolver types:**

| Type | Kab use karo |
|------|--------------|
| **DynamoDB resolver** | Data directly DynamoDB table se — sabse common, fast |
| **Lambda resolver** | Complex logic, jo bhi backend — full flexibility |
| **HTTP resolver** | External API ya ALB pe call |
| **RDS (SQL) resolver** | Relational data ke saath GraphQL |
| **OpenSearch resolver** | Search data |

**How it works:**
```
Client (React/Flutter/mobile)
     │  query { products { id name } }
     ↓
AppSync (GraphQL endpoint — managed server)
     │  Resolver (DynamoDB/Lambda)
     ↓
DynamoDB / Lambda / RDS / HTTP
     │  Response (sirf requested fields)
     ↑
Client — normalized cache + real-time subscriptions
```

**Setup steps (console):**
1. Console → AWS AppSync → "Create API" → "GraphQL API"
2. **Schema paste karo** (GraphQL SDL) ya Amplify se auto-generate
3. **Data source add karo** — DynamoDB table / Lambda function
4. **Resolver attach karo** — console pe mapping templates (Request/Response VTL) ya Lambda
5. **Auth mode** — API key (dev) / Cognito (users) / IAM
6. **Test** — Console Query editor me queries chalao
7. **Subscription enable** — client pe GraphQL client (Apollo/AWS Amplify) se connect

**AppSync vs API Gateway:**

| Feature | AppSync | API Gateway |
|---------|---------|-------------|
| API style | **GraphQL** (schema, queries) | REST/HTTP/WebSocket |
| Real-time | ✅ Subscriptions (built-in) | WebSocket endpoints (manual) |
| Offline sync | ✅ Built-in (Amplify) | ❌ |
| Data access | Resolvers (DynamoDB direct) | Lambda integrations |
| Use case | Mobile/web apps, real-time data | CRUD APIs, microservices, external |

**Kab kya?**
- **AppSync:** GraphQL chahiye, real-time data, offline mobile apps, app needs flexible query
- **API Gateway:** REST APIs, serverless CRUD, microservices integration, team REST me comfortable

**Pricing:**
- **Query/Mutation:** ~$4 per million requests (after free tier 250k/mo)
- **Realtime (subscriptions):** ~$1.25 per million minutes
- Data transfer + DynamoDB/Lambda extra

**Important points:**
- **AppSync is Amplify ka backend** — `amplify add api` → AppSync + DynamoDB (GraphQL mode)
- **Caching** — AppSync cache (TTL) — repeated queries fast + cost kam
- **Conflict resolution** — offline sync me versions → Lambda resolver
- **Resolvers tight karo** — authorization har field pe (Cognito groups)
- **Schema-first** — types pehle define karo phir resolvers
- **AppSync Events (nayi)** — high-scale pub/sub (realtime data without GraphQL mutations)
- AppSync + Amplify/GraphQL client — mobile SDK designed (React Native, Flutter, Swift)

**Analogy:** AppSync = **Buffet restaurant with recipe card** — aap chef (GraphQL schema) se kaho "mujhe sirf paneer aur salad chahiye" (query — sirf needed fields) — chef (AppSync) kitchen (DynamoDB/Lambda) se exactly wo laata hai — jo extra dish nahi (no over-fetch). Subscription = **Live kitchen cam** — dish (data) badla to aapko turant pata (real-time). API Gateway = **Fixed thali** — jo pakki list (endpoint) hai wahi milega.

## WorkSpaces (VDI — Virtual Desktop)

WorkSpaces AWS ki **managed virtual desktop (VDI)** service — jisse aap cloud me **Windows/Linux desktops** run karte ho. Employees apne PC/laptop/tablet se **remote login** karke apna **cloud desktop** use karte hain — OS, apps, files sab cloud me hain, device pe kuch install nahi. **Data device pe kabhi nahi jaata — security ke liye best.**

**Example — Amazon ka customer support team:**
1. Amazon ke call center agents ko har din khud ke desktop ki zaroorat hai
2. **WorkSpaces** pe 500 Windows desktops provision — Amazon Linux/Windows, MS Office, support apps installed
3. Agent apne kisi bhi device (thin client, laptop, tablet) se **WorkSpaces client** se login karta hai
4. Pura desktop cloud me chalta hai — agent ka device sirf screen stream karta hai
5. **Data (customer info) cloud me rehta hai** — agent ka USB/device data leak nahi ho sakta
6. IT team centrally sab desktops manage karti hai — patches, apps, policies — ek jagah

**WorkSpaces key features:**
- **OS options** — Windows 10/11, Amazon Linux 2, Ubuntu — choose karo
- **Bundles** — pre-configured CPU/RAM/storage combinations (Standard, Performance, Graphics)
- **Billing** — hourly ya monthly — pay-as-you-go
- **Multi-session** (workloads) — Windows 10 multi-user (one instance, many users) — cost kam
- **Workspaces Web/Client** — any device: Windows/Mac/iPad/Android/browser/Chromebook
- **Active Directory** — users/groups (AD connect) — SSO login
- **BYOL** (Bring Your Own License) — MS Office licence apna — cost low
- **Backup** — snapshots/restore
- **Persistence** — data sab WorkSpaces pe (EBS) — device wipe ho jaye to data safe
- **Security** — encryption (EBS+KMS), data stays AWS, device zero-trust

**WorkSpaces vs Citrix/VMware:**

| Feature | AWS WorkSpaces | Citrix/VMware (on-prem VDI) |
|---------|----------------|------------------------------|
| Setup | **Cloud-native — minutes** | Hardware + software infra pehle |
| Infrastructure | AWS handles | Aap khud servers kharidte ho |
| Cost model | Pay-as-you-go (hourly) | High Capex + Opex |
| Scaling | Auto-bandho (few clicks) | Capacity planning |
| Best for | Cloud-first, no data centers | Legacy on-prem VDI, existing investment |

**WorkSpaces vs EC2 (desktop use):**

| Feature | WorkSpaces | EC2 (RDP) |
|---------|-----------|-----------|
| Purpose | Desktops for users (VDI) | Servers |
| Login | User + AD, WorkSpaces client | SSH/RDP admin |
| Management | Console pe users/bundles | Full server admin |
| Cost | Per-user hourly/monthly | Per instance 24x7 |
| Best for | Employees, remote work | Dev/prod workloads |

**Components:**
- **WorkSpace** — ek user ka virtual desktop
- **Bundle** — desktop ka "spec" — OS + instance type (Standard 2vCPU/8GB, Performance, Graphics.g4dn)
- **Directory** — users ka source (AD Connector, Simple AD, AWS Managed AD)
- **Client** — desktop/laptop/mobile app ya browser (Web client)
- **Workspaces Pool (multi-user)** — users share underlying machines — cost optimized

**Setup steps:**
1. Console → Workspaces → **Create WorkSpaces**
   - Directory banao (Simple AD ya connect apna Active Directory)
   - Users add karo (AD users)
   - **Bundle** select karo (OS + size: Standard Windows 10, 2 vCPU/8GB)
   - Running mode: **AutoStop** (hourly billing, idle pe stop) ya AlwaysOn
2. **Invite users** — user ko username/password → client install (ya browser)
3. **Client login** — user apne device se → desktop ready
4. **Manage** — console: reboot, rebuild, restore, backup, terminate

**Pricing:**
- **Hourly (AutoStop):** sirf jitna use — idle pe auto-stop — example: Standard Windows ~$0.26/hr
- **Monthly:** fixed rate (~$32 Standard Windows) — no hourly
- Storage extra (user volume ~10-100GB)
- Workspaces Web/ browser — free additional
- BYOL windows license options — cost optimize

**Use cases:**
- **Remote work** — team ko desktops do — kisi bhi device se
- **Contractors** — temporary users ke liye desktops — hire/terminate fast
- **Secure data** — sensitive info se — data device pe aa hi nahi sakta
- **Dev/test environments** — developers ko linux/windows desktops
- **Legacy apps** — applications jo sirf Windows pe chalti hain — cloud me run

**Important points:**
- **Data local nahi** — device pe kuch bhi persist nahi (security)
- **AutoStop mode** — idle 60min → stop — cost cut (hourly billing)
- **Encryption** default on — by default encrypt EBS/KMS
- **Multi-session** pools — users same machine (Windows multi-session) — cost halved
- **AD zaroori** — users authentication ke liye
- **AppStream 2.0** (related) — apps streaming (bina full desktop) — whole desktop nahi chahiye to AppStream

**Analogy:** WorkSpaces = **Office desk in the sky** — workstation (PC, apps, files) cloud me ready hai — aap kisi bhi device se baith jao aur kaam karo. Device = **Sirf screen/monitor** (thin client) — asli computer cloud me hai. Data = office ke safe me — device chori ho jaye to kuch nahi jaata. EC2 = **Server room computer** — aap server ko RDP karte ho — WorkSpaces = **chaht waale desk** users ke liye ready-made.

## Amazon Q (AI Assistant)

Amazon Q AWS ki **generative AI assistant** hai — jo developers, IT aur business users ko AWS aur unke data pe help karti hai. **Do versions:**

| Version | Kiske liye | Kya karta hai |
|---------|-----------|---------------|
| **Amazon Q Developer** | Developers/DevOps | Code assistant (IDE pe), AWS hello help — code generate, debug, migrate, CLI |
| **Amazon Q Business** | Business users | Company data pe Q&A — docs, wikis, emails, connectors se |

**Example — Amazon ka developer, "ye Lambda error kyun?"**
1. Developer VS Code me Lambda code likh raha hai — error bhi mila
2. **Amazon Q Developer** (IDE extension / chat)
3. Q code context samajhta hai → error explain karta hai + **fix suggestion** deta hai
4. Q AWS **best practices** bhi batata hai — cost, security (IAM, encryption)
5. Business side: HR employee puchhta hai "HR policy kya hai?" → **Amazon Q Business** company docs se answer deta hai

**Amazon Q Developer kya karta hai:**
- **IDE/CLI chat** — VS Code, JetBrains, CLI me AI assistant
- **Code generation** — functions, tests, bugfixes inline
- **Code explanation & debug** — code maujida pe
- **AWS resource help** — aws CLI commands, CloudFormation/Terraform templates generate
- **`q` CLI** — terminal se: `q chat`, `q generate cloudformation`, `q translate`
- **Upgrade assistance** — Java upgrades (8→17/21), .NET, Windows server migratiions (auto-transform)
- **Code review / Agentic coding** — repo-level tasks, multi-file changes
- **Security scanning** (Q Developer security) — code me vulnerabilities

**Amazon Q Business kya karta hai:**
- **Company knowledge base** — docs, SharePoint, Confluence, S3, Salesforce connect (50+ connectors)
- **Q&A on company data** — "Leave policy", "Sales numbers Q1"
- **Permissions-aware** — employee ko sirf wo data milega jiski access hai (IAM/AD se)
- **Summaries/Reports** — meetings, emails summarize
- **App creation** — non-devs simple applications (wizard)

**Amazon Q vs ChatGPT — difference:**

| Feature | Amazon Q | ChatGPT |
|---------|----------|---------|
| GenAI base | AWS models (Bedrock connected) | OpenAI (ChatGPT) |
| Context | **AWS + aapka code/data** | Generic internet |
| AWS expertise | ✅ Deep (services, CLI, infra) | Limited |
| Company data access | ✅ Secure connectors (permissions) | ❌ (nhi hoti) |
| Data privacy | Enterprise secure (no training on data) | Depends on plan |
| Best for | AWS dev + business Q&A | General-purpose writing |

**Setup steps:**
1. **Q Developer (personal):**
   - VS Code → Extensions → "Amazon Q" install → AWS login (Builder ID)
   - Chat/Inline: Ctrl+I (inline), Ctrl+Enter (inline diff), select code → Ask Q
   - CLI: `pip install aws-q-developer-cli` (ya bundled) → `q chat`
2. **Q Business (company):**
   - Console → Amazon Q Business → create application
   - **DataSources connect** karo (S3, SharePoint, Confluence...)
   - **Index** banao (vector search data)
   - Permissions attach karo (IAM role, AD groups)
   - Users ko app/web interface do → login → company Q&A
3. **Q Developer (professional):** IAM Identity Center setup — enterprise policies

**Amazon Q + other AWS AI:**
- **Q = AI-assistant layer** (user-facing)
- **Bedrock = models (FMs) API** — apps use karein
- **SageMaker = train/deploy your own models**
- Q Developer **uses Bedrock models** internally — aapko FMs directly nahi dikhte

**Pricing:**
- **Amazon Q Developer Pro:** ~$19/user/month
- **Amazon Q Business:** ~$3/user/month (Q in QuickSight) ya ~$20/user/month (full)
- Free tiers/dev offers exist — check current AWS page

**Important points:**
- Q Business **doesn't train on your data** — enterprise privacy (AWS guarantee)
- **Permissions-aware** — sirf authorized data answer
- Q Developer security scanning — separate add-on
- Budget: 1000 lines/month free (developer tier) — check
- For students/devs — **Q Developer $0** (Builder ID) use karo

**Analogy:** Amazon Q = **AWS-specialized copilot** — aapke IDE ke andar expert jo AWS ke 200+ services jaanta hai aur aapka code bhi padh sakta hai. ChatGPT = **General encyclopedia** — sab kuch pata hai thoda-thoda. Q Business = **Company HR+helpdesk combined** — jo sirf authorized company docs ke mutabik jawab deta hai — data safe.

## MCP (Model Context Protocol) for AWS

MCP ek **open standard (Anthropic ka)** hai jo **AI assistants (LLMs) ko tools aur data se connect** karta hai — ek standardized tarike se. **"AI ka USB-C port"** — jaise ek port se sab devices connect, waise MCP se koi bhi AI assistant kisi bhi tool/data source se connect. **AWS ke official MCP servers (awslabs/mcp)** AI coding assistants ko AWS documentation, APIs, pricing, aur service-specific context dete hain — taake AI assistant AWS ko sahi tarike se use kare, live data ke saath.

**MCP architecture:**
```
AI Assistant (Claude, Cursor, VS Code, Amazon Q, opencode)
     │  MCP Client
     ↓  (standardized protocol)
MCP Server (AWS Documentation / API / Pricing...)
     ├── Tools (functions AI call kar sakta hai)
     ├── Resources (data/files)
     └── Prompts (templates)
             ↓
          AWS APIs / Docs / Services
```

**MCP core concepts:**

| Concept | Kya hai |
|---------|---------|
| **MCP Server** | Lightweight program jo tools expose karta hai (e.g., "AWS docs search") |
| **MCP Client** | AI assistant ka hissa — server se 1:1 connect |
| **Tools** | Functions AI call kar sakta hai (jaise `read_documentation`) |
| **Transport** | Stdio (local CLI) ya Streamable HTTP (remote) |
| **Host** | ChatGPT/Claude Desktop/IDE/code agent jahan AI chalta hai |

**AWS ke official MCP servers (awslabs/mcp):**

| Server | Kya tools deta hai |
|--------|--------------------|
| **aws-documentation-mcp-server** | AWS docs read/search — pages Markdown me convert, sections, tables, recommendations |
| **aws-api-mcp-server** | AWS CLI commands over AWS APIs — AI se AWS manage/query (⚠️ powerful — security dhyan) |
| **aws-cost-analysis-mcp-server** | AWS Pricing + Cost Explorer — pricing queries ("EC2 m5.large kitna us-east-1 me") |
| **aws-bedrock-mcp-server** | Bedrock FMs — invoke models, list models, context |
| **aws-serverless-mcp-server** | Lambda/Fargate guidance — deploy, best practices |
| **aws-ecs-mcp-server** | ECS — clusters/containers guidance, deployments |
| **aws-eks-mcp-server** | EKS — Kubernetes clusters, kubectl workflows |
| **aws-neptune-mcp-server** | Neptune databases — Gremlin/SPARQL queries |
| ... (list badhti jaati hai) | |

**Installation example (VS Code + Documentation MCP server):**
```bash
# pip se install karo
pip install awslabs.aws-documentation-mcp-server
# Server chalao (stdio transport):
aws-documentation-mcp-server
```
VS Code me (`mcp.json`):
```json
{
  "servers": {
    "aws-docs": {
      "type": "stdio",
      "command": "aws-documentation-mcp-server"
    }
  }
}
```
Ya Cursor/Claude Desktop settings me add karo.

**Amazon Q Developer + MCP:**
- Amazon Q Developer ne **MCP client support** add kiya hai — external MCP servers se connect kar sakta hai (repo scope me config)
- Q kisi custom MCP server (apne internal tools) se bhi connect — enterprise workflows

**MCP use cases:**
- **Vibe coding** — AI assistant AWS resources banae (recommended guidance with MCP servers)
- **AWS docs assistant** — "S3 lifecycle policy kaise likhe?" → server exact docs se accurate answer
- **Cost optimization AI** — pricing queries live data se
- **Infra management AI** — API server se resources inspect/manage (careful — production me IAM scoped roles)

**MCP vs API (LLM function calling):**

| Feature | MCP | Direct APIs |
|---------|-----|-------------|
| Standard | ✅ Open standard (universal) | ❌ Har vendor alag |
| Integration | One-time config — koi bhi client | Har client pe custom code |
| Tools/context | Tools + resources + prompts ek framework | Sirf API calls |
| Ecosystem | Growing — AWS, GitHub, Slack... | Manual |

**Important points:**
- **Security — sabse zaroori:** MCP tools AI ko real actions karwa sakte hain (API server) — **prompt injection risks** — untrusted data se careful
- **Least privilege IAM** — API MCP server ko sirf required permissions
- AWS MCP servers **open source** (Apache-2.0) — self-host kar sakte ho
- Docs server **read-only** (safe) — API server **read-write** (powerful but risky)
- Local (stdio) vs Remote (HTTP) — remote production ke liye auth chahiye
- Bedrock Agents / Event MCP — AWS services pe MCP ecosystem expand

**Analogy:** MCP = **USB-C port for AI** — pehle har device (tool) ka apna charger (API) hota tha; ab ek standard port — koi bhi AI assistant (phone) kisi bhi device (AWS services/docs/tools) ko plug-in karke use kar sakta hai. AWS MCP servers = **USB-C adapters for AWS** — AWS ka poora ecosystem AI assistants ke liye plug-and-play.

## IAM (Identity & Access Management)

IAM AWS ki **permission and identity management** service — jo control karti hai **kaun (identity) kya kar sakta hai (permissions) kisi bhi AWS resource pe**. Root account ke andar **users, groups, roles, policies** banake access manage karte ho. **Security ka foundation** — AWS me sab kuch IAM se hi hota hai.

**Example — Amazon ka team:**
Amazon ki team me 3 log:
- **Dev:** Lambda/S3 resources bana sakta hai — sirf dev environment
- **Ops:** Server restart, logs dekh sakta hai — prod pe bhii
- **Finance:** Sirf cost reports (Billing) — koi resource nahi

1. IAM me **3 users** (dev, ops, finance) + **groups** (Developers, Operators, Finance)
2. Har group pe **policies**: "Developers → Lambda + S3 access", "Finance → readonly Billing"
3. User ne galat kaam kiya? Policy tight karo ya user remove
4. Security: **MFA**, password policy, **access keys** limited

**IAM core concepts:**

| Concept | Kya hai |
|---------|---------|
| **User** | Ek person/service ka login (long-term credentials) |
| **Group** | Users ka set — ek policy group pe laga do, sabko milegi |
| **Role** | Temporary credentials — services/apps ke liye (short-term) |
| **Policy** | Permissions ka document (JSON) — Allow/Deny |
| **Permission boundary** | User/role ki max limit (safety cap) |
| **Access key** | CLI/SDK ke liye (Access Key ID + Secret) — rotate karo |
| **MFA** | Multi-factor authentication — extra security |
| **Identity Provider (IdP)** | External login (Google Workspace, Microsoft Entra ID, company SSO) |

**Policy (JSON) example:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-app-assets",
        "arn:aws:s3:::my-app-assets/*"
      ]
    },
    {
      "Effect": "Deny",
      "Action": "s3:DeleteBucket",
      "Resource": "*"
    }
  ]
}
```
- **Effect:** Allow / Deny
- **Action:** kaunse operations (`s3:GetObject`, `ec2:StopInstances`)
- **Resource:** kin cheezon pe (ARN — bucket, instance, `*` = sab)
- **Condition** (optional): IP, time, MFA required, tags

**Policy types:**

| Type | Kya hai |
|------|---------|
| **Managed policies** | AWS ready (Admin, ReadOnly) ya customer-created — multiple users attach |
| **Inline policies** | Bir user/role pe directly (custom) |
| **Resource-based** | Resource pe laga hota hai (S3 bucket policy, role trust policy) |
| **Identity-based** | User/group/role pe attached |

**Users vs Roles — kab kya?**

| Feature | User | Role |
|---------|------|------|
| Eligible | Humans (long-term credentials) | Services/apps (temp credentials) |
| Credentials | Password + Access keys (static) | **STS temp keys** (auto-expire) |
| Example | Developer login console/CLI | EC2 → S3 read, Lambda → DynamoDB |
| Best practice | Minimum users — SSO/IdP prefer karo | **Hamesha roles** — code me keys nahi |

**Roles — services ke liye:**
- **EC2 role:** IAM role attach → EC2 S3 access (no keys in instance)
- **Lambda role:** function ki permissions
- **Service-linked roles:** AWS services apne liye (auto-created)

**Trust policy example (role):**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": { "Service": "ec2.amazonaws.com" },
    "Action": "sts:AssumeRole"
  }]
}
```

**IAM best practices:**
- **Root account sirf root ke liye** — MFA + NEVER daily use
- **Least privilege** — jitni zaroorat utni hi permission
- **Groups use karo** — users pe directly mat laga
- **Roles use karo** — code me keys mat rakho
- **MFA** — har user par
- **Access keys rotate** karo + unused keys delete
- **Password policy** strong (length, expiry)
- **Audit** — IAM Access Analyzer, CloudTrail se grant issues

**IAM Identity Center (SSO):**
- Ek hi login se multiple accounts/regions (enterprise)
- Company AD/Entra ID se connect — users kay hi set
- **Permission sets** — same permissions multiple accounts me apply
- Replacement of "users groups ke siwa bahut saare users"

**CLI examples:**
```bash
# User banao + console password
aws iam create-user --user-name dev-user
aws iam attach-user-policy \
  --user-name dev-user \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess

# Group + policy
aws iam create-group --group-name Developers
aws iam add-user-to-group --group-name Developers --user-name dev-user
aws iam attach-group-policy \
  --group-name Developers \
  --policy-arn arn:aws:iam::aws:policy/AWSLambda_FullAccess

# Role (EC2 → S3)
aws iam create-role --role-name ec2-s3-role \
  --assume-role-policy-document file://trust-policy.json
aws iam attach-role-policy \
  --role-name ec2-s3-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
```

**Setup steps (console):**
1. Console → IAM → **Users** → "Create user" → name + access type (programmatic/console)
2. Permissions: group se attach (ya policy directly)
3. **Access keys** generate → CLI config (`aws configure`)
4. **MFA device** add karo (recommended)
5. **Roles** — IAM → Roles → Create → trust entity (EC2/Lambda/service) → permissions
6. **Test karo** — as user login → apna access check

**Pricing:** IAM **free** — sirf resource you interacting wale cost.

**Analogy:** IAM = **Office security system + badge card** — har employee (user) ka badge (credentials) hai, departments (groups) me badges, aur security rules (policies) batati hain kaun kis room (resource) me ja sakta hai. Role = **Visitor pass** — contractor (EC2 app) ko temporary pass — kuch ghar (S3) me allowed, ghar ki key (access keys) permanent nahi. MFA = **Card + fingerprint** — do factor. Least privilege = sirf wahi rooms kholo jo kaam ke liye chahiye.

## Identity Center (IAM Identity Center / SSO)

IAM Identity Center (pehle **AWS SSO**) AWS ki **centralized access management** service — jo **ek hi login (SSO)** se users ko **multiple AWS accounts, applications aur permissions** deti hai. Company ke scale pe — jab doosre IAM users management bhaut bhaari ho jata hai — Identity Center sab kuch ek jagah se manage karta hai.

**Example — Amazon ka company (100 employees, 50 accounts):**
1. Amazon ke paas 50 AWS accounts hain (dev, prod, finance, hr...)
2. 100 employees ko alag alag access chahiye har account me
3. **IAM users** se to 100×50=5000 users banane padte ❌
4. **Identity Center** se: users ek jagah (company directory), aur har account me **permission sets** (roles) — ek user ko assign karo
5. **Ek login** — user ek baar login karta hai → saare assigned accounts/apps dikhte hain → click → access
6. Company AD/Entra ID se connect — users auto-sync — no duplicate users

**Core concepts:**

| Concept | Kya hai |
|---------|---------|
| **Identity Center enabled** | SSO setup — users dir + instance |
| **Users & Groups** | Identity Center me manage (ya AD/Entra se sync) |
| **Permission Sets** | Roles ka re-usable set (jaise "Admin", "ReadOnly", "EC2-Admin") |
| **AWS accounts** | Accounts assign karo → permission set attach karo user/group |
| **Applications** | Third-party apps (Salesforce, Office 365) SSO se connect |
| **Assignment** | User/group → account + permission set |
| **Access portal URL** | User ka singleton login page (`d-xxxx.awsapps.com/start`) |

**Architecture:**
```
Users (Identity Center dir / AD / Entra ID)
     ↓ SSO (single login — portal)
Identity Center (assignments + permission sets)
     ├── AWS Account 1 → Permission Set A (Admin)
     ├── AWS Account 2 → Permission Set B (ReadOnly)
     ├── AWS Account 3 → Permission Set C (EC2-Admin)
     └── Apps (Salesforce, O365, GitHub) → SSO SAML
```

**Permissions sets kya hote hain:**
- **Permission set** = policy bundle + session duration (jo role user account me assume karta hai)
- Managed policies ya custom (JSON) use karo
- Same permission set ko multiple accounts me attach → consistency
- Example:
  - `Dev-Access`: Lambda + S3 read/write (dev accounts ke liye)
  - `Prod-ReadOnly`: sab resources read
  - `Billing`: sirf billing + cost explorer

**How login works (user side):**
```
User → access portal URL → login (email/password + MFA)
     ↓
List: Account 1 (Admin) / Account 2 (ReadOnly) + Apps
     ↓
Click → STS assume role → console (ya CLI/API via SSO)
```
CLI: `aws sso login` → credentials auto-config — `aws sts get-caller-identity`

**Identity Center vs IAM users:**

| Feature | Identity Center (SSO) | IAM Users |
|---------|----------------------|-----------|
| Scale | **100s users / 100s accounts** easy | Painful (har account me users) |
| Login | **Ek login** — saare accounts | Har account ka alag login |
| MFA/SSO | Central (AD/Entra, SAML, Okta) | Individual |
| Groups | Central groups + assignments | Per-account groups |
| Best for | **Companies, multi-accounts, enterprise** | Single account, small teams, scripts |

**Setup steps:**

1. **Identity Center enable karo** — Console → IAM Identity Center → "Enable" (instance auto banata hai)
   - Recommended: AWS Organizations (multi-account)
2. **Identity source chuno:**
   - **Identity Center directory** (built-in — Google/Entra sync supported) 
   - **Active Directory** (AWS Managed AD / on-prem AD Connector)
   - **External IdP** (Okta, Entra ID, Ping — SAML 2.0)
3. **Users/groups banao** (ya AD se sync)
4. **Permission sets banao:** Permission sets → "Create" → name + policies attached (AdministratorAccess ya custom)
5. **Assign karo:** AWS accounts → select account → permission set → users/groups
6. **User ko portal URL do** — user login → accounts dikhte hain
7. **Default SSO** for console/CLI + `aws sso login` configure

**CLI with Identity Center:**
```bash
# Console login first; CLI:
aws configure sso
# SSO session name, start URL, region, account, role → ok
aws sso login   # browser login
aws sts get-caller-identity  # check
```

**Important points:**
- **Free service** — Identity Center koi extra cost nahi (AWS Organizations me)
- **Permission sets** = session duration (default 1h) — short sessions best practice
- **SCIM sync** — AD/Entra users auto provision
- **Access Analyzer** — external access review (compliance)
- **Attribute-based access control (ABAC)** — tags se permissions (dynamic)
- **Service control policies (SCP)** — account-level limits (Organizations) — Identity Center ke saath deep
- Identity Center **replaces IAM users** for humans — IAM users sirf service-to-service/scripts ke liye

**Analogy:** Identity Center = **Company main gate with master badge** — ek baar badge (login) se saari buildings (accounts) ke saare allowed rooms (permission sets) khulte hain. IAM users = har building ka alag alag guard/lock. Permission set = "Master key for Building A, Visitor pass for B". Portal = **Company directory** — jahan sab buildings ka list + ek click me entry.

## VPC (Virtual Private Cloud) — Networking

VPC AWS ka **cloud networking** service — jisme aap apna **private virtual network** banate ho (jaise apne on-prem data center me network) — with **IP ranges, subnets, route tables, gateways, firewalls**. India me analogy: aapka **own gated community** — andar apni buildings (subnets), streets (route tables), security (security groups/NACLs), aur bahar ka connection (internet gateway).

**Example — Amazon ka web app network:**
Amazon ka app 3-tier structure me chalta hai:
```
VPC (10.0.0.0/16)
├── Public Subnet (10.0.1.0/24) — Internet Gateway
│   └── ALB / Web servers (internet se accessible)
├── Private Subnet (10.0.2.0/24) — no direct internet
│   └── App servers (EC2)
└── Private Subnet (10.0.3.0/24) — DB zero internet
    └── RDS database (sirf app se)
```
1. Users internet se aate hain → **Internet Gateway (IGW)** → **Public subnet** (ALB) 
2. ALB traffic **app servers** ko bhejta hai (private subnet)
3. App servers **RDS** se data lete hain (database subnet — internet se completely hidden)
4. **NAT Gateway** — private subnet apps ko outbound internet deta hai (packages/downloads) bina inbound ke

**VPC core components:**

| Component | Kya hai |
|-----------|---------|
| **VPC** | Aapka private network (IP CIDR: `10.0.0.0/16`) |
| **Subnet** | VPC ka hissa — ek AZ me (public/private) |
| **Route table** | Traffic kahan jayega (0.0.0.0/0 → IGW / NAT / local) |
| **Internet Gateway (IGW)** | VPC ↔ internet ka bridge (public access) |
| **NAT Gateway/Instance** | Private subnet ka outbound internet (no inbound) |
| **Security Group (SG)** | **Instance-level firewall** (stateful) — allow-only rules |
| **Network ACL (NACL)** | **Subnet-level firewall** (stateless) — allow/deny rules |
| **VPC Peering** | Do VPCs ek-dusre se connect (same/other account) |
| **VPN / Direct Connect** | On-prem → AWS private connection |
| **VPC Endpoint** | AWS services (S3/DynamoDB) ko internet ke bina access |
| **Gateway LB / Transit Gateway** | Large scale networking (hub for many VPCs) |

**CIDR & IP basics:**
- **CIDR notation:** `10.0.0.0/16` = 65,536 IPs; `/24` = 256 IPs; `/28` = 16 IPs
- AWS VPC: max `/16`, min `/28`
- Har subnet me **5 IPs reserved** (network, gateway, DNS, future use) — 10.0.1.0/24 se 251 usable
- **AZs (Availability Zones)** — subnets alag AZ me daalo (11 regions me 3+ AZs) — high availability

**Public vs Private subnet:**
| Feature | Public Subnet | Private Subnet |
|---------|---------------|----------------|
| Internet access (inbound) | ✅ IGW se | ❌ (no route to IGW) |
| Outbound | ✅ IGW | ✅ NAT se (ya direct) |
| Resources | ALB, web, bastion host | DB, app servers, backend |
| Security | Less (exposed) | More (hidden) |

**Route table example:**
```
Destination       Target
10.0.0.0/16       local          (VPC ke andar)
0.0.0.0/0         igw-xxxx       (public subnet — internet se)
0.0.0.0/0         nat-xxxx       (private subnet — NAT se outbound)
10.0.5.0/24       pcx-xxxx       (peering — doosra VPC)
```

**Security Group vs NACL:**

| Feature | Security Group | Network ACL |
|---------|---------------|-------------|
| Level | **Instance** level | **Subnet** level |
| Stateful/Stateless | ✅ Stateful (return auto-allow) | ❌ Stateless (in+out dono rules) |
| Rules | Sirf **Allow** | Allow **+ Deny** |
| Default | Deny all (empty) | Allow all |
| Order | No order (all apply) | Rule numbers (1-32766, lowest first) |
| Analogy | Building ka room guard | **Society gate guard** (pehle check — phir room) |

**Setup steps — 3-tier VPC (console):**
1. **VPC banao** — Console → VPC → "Create VPC" — name + CIDR (`10.0.0.0/16`) — IPv4 only (ya IPv6 extra)
2. **Subnets banao** — 3-4 subnets:
   - Public: 10.0.1.0/24 (us-east-1a), 10.0.2.0/24 (us-east-1b)
   - Private app: 10.0.3.0/24 (a), 10.0.4.0/24 (b)
   - Private DB: 10.0.5.0/24 (a), 10.0.6.0/24 (b)
3. **IGW** — create → VPC se attach
4. **Route tables** — public RT: `0.0.0.0/0 → IGW` (public subnets attach), private RT: `0.0.0.0/0 → NAT`
5. **NAT Gateway** — public subnet me (EIP ke saath) → private RT me route
6. **Security Groups**:
   - ALB SG: 0.0.0.0/0 : 80/443
   - App SG: ALB SG se : 80 (source = SG — secure!)
   - DB SG: App SG se : 3306/5432
7. **Test** — EC2 launch: public me IGW + private me NAT se internet check

**VPC Peering:**
```
VPC A (10.0.0.0/16) ⇄ pcx-xxx ⇄ VPC B (10.0.5.0/16)
```
- Same/different account/region — private IP communication (fast, no internet)
- **No transitive peering** — A↔B, B↔C se A↔C nahi (Transit Gateway chahiye)

**VPC Endpoint (private AWS access):**
- **Gateway endpoint** — S3/DynamoDB (free, route-level)
- **Interface endpoint** — baaki services (private ENI in VPC)
- Internet ke bina S3/DynamoDB access — security + no data egress cost

**Important points:**
- **Default VPC** — har account me pre-made (ok for learning, aur production me custom banao)
- **Flow Logs** — VPC network traffic logs (CloudWatch) — security monitoring
- **Multi-AZ** hamesha — 2 AZs minimum (HA)
- **Route propagation** — VPN/DX se auto routes
- **IPv6** support — dual-stack subnets
- **VPC pe 5 IPs reserved** — subnet design me dhyan rakhna
- **Kaam ke standard patterns** — public/private/database subnets (3-tier) — well-architected default

**Pricing:** VPC **free** — sirf NAT Gateway (~$0.045/hr) + data transfer (outbound) + VPC endpoints (hourly) ka cost. IGW, subnets, route tables, SG, VPC free.

**Analogy:** VPC = **Gated community** — poora society (VPC) ka ek fence (CIDR), andar blocks (subnets), roads (route tables), gate (IGW), guards (SG/NACL). Public block = **Front shops** (maine road pe — sabko accessible). Private block = **Ghar** — andar ka traffic sirf guards (SG) se allowed. NAT = **Security kiosk** — residents (private) bahar jaa sakte hain lekin outsiders andar nahi. Peering = **Two societies ka private shortcut road**. Endpoint = **Society ki internal post office** — bahar ki post (internet) ke bina items (S3) deliver.

## VPN (Site-to-Site & Client VPN)

VPN AWS ki **encrypted private connection** services hain jo **aapke on-premises data center / company office ko AWS VPC se secure** connect karti hain — internet ke encrypted tunnel me. **DoS tarah:**

| Type | Kya connect karta hai | Use Case |
|------|----------------------|----------|
| **Site-to-Site VPN** | Office/DC ↔ AWS VPC (IPsec) | Company ka data center se AWS |
| **Client VPN** | Individual employees (laptop) ↔ AWS | Remote teams — kisi bhi jagah se |

**Example — Amazon ka office + AWS hybrid:**
Amazon ka office Mumbai me hai, infra AWS me:
1. **Site-to-Site VPN** — office ka **VPN gateway** ↔ AWS **Virtual Private Gateway (VGW)** — IPsec tunnel
2. Office ke employees AWS resources (private subnets) ko **direct access** karte hain — jaise on-prem network me ho
3. Traffic **encrypted** jaata hai — koi intercept nahi kar sakta
4. **Client VPN** — remote employee (delhi me) laptop se `AWS Client VPN` connect → office/AWS resources
5. **Direct Connect** (alag) — private dedicated line (no internet) — VPN se faster/stable for production

**Site-to-Site VPN components:**
```
Office (on-prem)
  ├── Customer Gateway (CGW) — aapka VPN device (on-prem)
  └── IPsec tunnel (2 tunnels — HA)
            ↓
AWS VPC
  ├── Virtual Private Gateway (VGW) — AWS side endpoint
  ├── Route table (0.0.0.0/0 → VGW for traffic) 
  └── Target: private subnets
```

**Site-to-Site VPN setup steps:**
1. AWS side: **Virtual Private Gateway (VGW)** banao + VPC pe attach
2. **Customer Gateway (CGW)** — on-prem VPN device ka info (public IP, BGP options) add karo
3. **VPN Connection** banao — VGW + CGW → 2 tunnels
4. On-prem router pe **config download** karo (Cisco/Juniper/OpenVPN formats) → apply
5. VPC route table me on-prem CIDR → VGW route add karo
6. On-prem side bhi AWS CIDR → VPN route
7. Test: ping/connect private instance

**Client VPN setup steps:**
1. **Client VPN endpoint** banao (VPC me, subnets + SG)
2. **Certificates** — server + client certs (ACM ya self-signed)
3. Auth: certificates ya **SAML** (downstream IdP — company SSO)
4. Association — subnets se attach + route add
5. Users: **AWS Client VPN app** (Windows/Mac/Linux) → config file → connect

**VPN vs Direct Connect:**

| Feature | Site-to-Site VPN | Direct Connect |
|---------|------------------|----------------|
| Connection | **Internet pe encrypted tunnel** | **Dedicated private line** (no internet) |
| Speed | Depends on internet (10-100Mbps-ish real) | **1-100 Gbps** |
| Latency | Variable | **Consistent low** |
| Cost | Cheap (per connection/hour) | Expensive (port + line charges) |
| Reliability | Internet issues se affected | Highly reliable (SLAs) |
| Setup | Hours | Weeks (telco line) |
| When | **Start/small**, backup link | Production, large data transfer, low latency |

**VPN pricing:** per VPN connection per hour (~$0.05/hr) — plus data transfer (no charge for VPN tunnel egress to on-prem, ingress free), Client VPN per endpoint + per connection aur.

**Important points:**
- **2 tunnels** hamesha (HA) — ek fail doosra sambhalta
- **BGP** dynamic routing (or static routes) — BGP better for failover
- **NAT/PAT overlap** careful — CIDR overlap (same IP range on-prem + VPC) = merge before
- VPN + Direct Connect dono = **regional + backup path** (best practice)
- Client VPN → **split tunnel** option — sirf AWS traffic VPN se, baaki direct internet (cost kam)
- **Transit Gateway VPN** — multiple VPCs/on-prem hub-spoke

**Analogy:** Site-to-Site VPN = **Company buildings ka private tunnel** — do buildings (office ↔ AWS) ke beech sealed underground tunnel (IPsec) — sirf employees (resources) andar. Client VPN = **Personal security tunnel** — remote employee jahan bhi ho, uske laptop se company network ka safe tunnel. Direct Connect = **Dedicated highway** — koi traffic light nahi, fastest — but construction me time lagta hai.

## PrivateLink (AWS PrivateLink)

PrivateLink AWS ki **private service connectivity** — jisse aap **internet ke bina** do VPCs/services ko connect karte ho — **private IPs pe, pehle se built security**. AWS services (S3, DynamoDB, APIs, saath hi third-party services) ko aapke VPC ke andar jaise private endpoint se access karte ho. **Koi data public internet/customer VPC se pass nahi hota.**

**Problem — without PrivateLink:**
```
VPC A (service)        VPC B (consumer)
  └── service app          └── access?
        ↓                        ↓
     Public IP/ALB       internet pe (insecure)
```
- Service public karni padti hai (security risk)
- Ya peering (transitive nahi + centralized management mushkil)

**PrivateLink solution:**
```
VPC A (service provider)
  └── NLB/ALB + PrivateLink service
          ↓ (endpoint service)
VPC B (consumer)
  └── VPC Endpoint (interface — private ENI in B's subnet)
          ↓
      Service ko private IP se access — no internet
```

**Core concepts:**

| Concept | Kya hai |
|---------|---------|
| **Endpoint Service** | Service provider ka published service (NLB/ALB/GWLB ke peeche) |
| **VPC Endpoint (Interface)** | Consumer side ka private ENI — service connect |
| **Gateway Endpoint** | S3/DynamoDB ke liye (route-based, free) |
| **Private DNS** | Customer side DNS names → private IPs resolve |
| **Consumer principal** | IAM me "aws:SourceVpce" — trusted consumers only |

**PrivateLink use cases:**
- **SaaS/services** — apni service doosre accounts/VPCs ko private access do (marketplace listing)
- **AWS services private access** — S3, DynamoDB, API Gateway, CloudWatch, Lambda (via interface endpoints)
- **VPC-to-VPC service access** — peering ke bina service sharing (no transitive issue)
- **Security/Compliance** — data never touches internet — zero exposure

**PrivateLink vs VPC Peering:**

| Feature | PrivateLink | VPC Peering |
|---------|-------------|-------------|
| Scope | **Service-to-VPC** (endpoint service) | **VPC-to-VPC** (full network) |
| Transitive | ✅ (every VPC apna endpoint) | ❌ |
| Access | Sirf specific service/endpoints | Poora CIDR visible |
| Security | Private endpoint only — isolated | Full network peering |
| Best for | Sharing ONE service with many VPCs | Two VPCs ko poori connectivity |

**PrivateLink vs NAT/IGW (internet):**

| Feature | PrivateLink | Internet (NAT/IGW) |
|---------|-------------|---------------------|
| Security | **No public exposure** | Public IP involved |
| Data path | Private network | Internet |
| Compliance | ✅ (no data egress) | ❌ (data egress cost + risk) |
| Cost | Hourly endpoint + data | NAT cost + data transfer |

**Setup steps (service provider):**
1. **NLB (ya ALB/GWLB)** banao — service behind load balancer
2. Console → VPC → **"Create endpoint service"** — NLB select karo
3. Service name bante hai (com.amazonaws.vpce.<region>.<id>)
4. **Allowlist principals** — kaunse accounts access kar sakte (ARN list)
5. Notifications (SNS) — attach approve/reject requests

**Setup steps (consumer):**
1. Console → VPC → "Create endpoint" → **Interface** type
2. Service name paste karo (ya choose from list)
3. Subnets select (private) + security group
4. **Private DNS** enable (service hostname → private IP)
5. Access IAM check (endpoint policy)

**Pricing:** per endpoint **hourly** + **data processed per GB** (endpoint + service side). Gateway endpoints — S3/DynamoDB — **free** (no hourly).

**Important points:**
- **`aws:SourceVpce`*/`aws:SourceVpc`** conditions — IAM me trusted endpoints enforce (security)
- Interface endpoints require **subnet + SG** (like ENI)
- Endpoint **policies** — consumer side restriction (jaise S3 endpoint = prefix-only)
- PrivateLink + **Transit Gateway** — on-prem se bhi private services access
- **Cross-account allowed** — approval flow (reject/accept)
- **Region bound** — endpoints region me local (cross-region nahi default)

**Analogy:** PrivateLink = **Private courier connection** — aapke ghar (VPC) se doosri company (service) tak ek **confirmed door-to-door courier** (endpoint) jo sirf aapka hi lekin security-checked package (data) delivery karta hai — kisi public road (internet) se nahi. Peering = full road between two societies. Gateway endpoint = **Society ka baaja office** (free) for local S3 mail. NAT/IGW = **Bahar ki delivery** — internet pe — data sabse open.

**CDK best practices (quick):**
- Small focused stacks (poora infra ek stack me mat dalo)
- `cdk diff` review hamesha — accidental changes se bacho
- Snapshot testing (cdk assertions) — infra tests likho
- Tags lagao — cost tracking



