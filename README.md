# شرح الكابرنتيس (Kubernetes Guide)

## مقدمة | Introduction

الكابرنتيس (Kubernetes) هو نظام مفتوح المصدر لأتمتة (اوركسترة) حاويات الدوكر (Docker containers) عبر مراكز بيانات متعددة.

**Kubernetes** is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications across clusters of machines.

---

## مبادئ أساسية | Core Concepts

### 1. **المينيباع (الوحدة الأساسية) - Pod**
- أصغر وحدة قابلة للنشر في Kubernetes
- يمكن أن تحتوي على واحد أو أكثر من حاويات Docker
- تشترك جميع الحاويات في شبكة واحدة و Volume واحد
- The smallest deployable unit in Kubernetes
- Can contain one or more containers (usually Docker containers)
- Containers in a pod share networking and storage resources

### 2. **الخدمة (Service)**
- طريقة لكشف النطاق (expose) لتطبيقات Pod
- توفر عنوان IP ثابت و DNS اسم
- توزيع الحمل (load balancing) عبر Pods
- A way to expose applications running on Pods
- Provides stable IP addresses and DNS names
- Distributes traffic across multiple Pod replicas

### 3. **مراقب (عقدة) - Node**
- ماكينة العمل الفيزيائية أو الافتراضية
- تشغل واحد أو أكثر من Pods
- موضع رئيسي أو موضع عامل (Master or Worker)
- A worker machine (physical or virtual) that runs pods
- Can be on-premises or cloud VMs
- Managed by the control plane

### 4. **مجموعة (Cluster)**
- مجموعة من Nodes المرتبطة ببعضها
- تدار من قبل Control Plane
- A set of connected nodes running containerized applications
- Managed by a control plane (Master)
- Can span multiple data centers

---

## معمارية Kubernetes | Architecture

```
+---------------------+
|   Control Plane     |
| (Master Node)       |
+-----+-------+-------+
  |   |   |   |
  |   |   |   +-> Scheduler
  |   |   +------> Controller Manager
  |   +----------> API Server
  +---------------> etcd (Database)

  |               |
  |               |
+-----+       +-----+
|     |       |     |
|   Worker   Worker   |
|   Node 1   Node 2   |
+-----+       +-----+
  | Pods        | Pods
```

### **Control Plane Components**

1. **API Server**: واجهة الاتصال الرئيسية مع Kubernetes
2. **Scheduler**: يختار Nodes لتشغيل الـ Pods الجديدة
3. **Controller Manager**: يشغل control loops المختلفة
4. **etcd**: قاعدة البيانات المركزية لحفظ حالة Cluster

### **Worker Node Components**

1. **kubelet**: يضمن تشغيل Pods على النحو المطلوب
2. **Container Runtime**: Docker أو Containerd
3. **kube-proxy**: يدير الشبكات على Node

---

## موارد Kubernetes | Kubernetes Resources

### **Deployment**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app-container
        image: my-app:1.0
        ports:
        - containerPort: 8080
```

### **Service**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
  type: LoadBalancer
```

### **ConfigMap و Secrets**
- **ConfigMap**: لتخزين البيانات غير الحساسة (متغيرات البيئة)
- **Secrets**: لتخزين البيانات الحساسة (كلمات المرور، tokens)

---

## عمليات آساسية | Basic Operations

### **إنشاء Deployment**
```bash
kubectl apply -f deployment.yaml
```

### **عرض Pods**
```bash
kubectl get pods
kubectl get pods -o wide  # معلومات مفصلة
```

### **عرض Deployments**
```bash
kubectl get deployments
```

### **عرض الخدمات**
```bash
kubectl get services
```

### **تحديث Deployment**
```bash
kubectl set image deployment/my-app app-container=my-app:2.0
```

### **حذف Deployment**
```bash
kubectl delete deployment my-app
```

### **عرض سجلات Pod**
```bash
kubectl logs pod-name
kubectl logs pod-name -f  # المتابعة المباشرة
```

---

## التوسع (Scaling)

### **الموارد (Resources)**
- CPU: معالج
- Memory: ذاكرة
- Storage: تخزين

### **تغيير عدد Replicas**
```bash
kubectl scale deployment my-app --replicas=5
```

### **Auto Scaling**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80
```

---

## الفوائد الرئيسية | Key Benefits

✅ **توزيع الحمل التلقائي (Automatic Load Balancing)**
✅ **إعادة جدولة تلقائية (Auto Rescheduling)**
✅ **تحديث بدون توقف (Rolling Updates)**
✅ **التوسع التلقائي (Auto Scaling)**
✅ **إدارة الموارد (Resource Management)**
✅ **التشفير والأمان (Security)**

---

## مراجع إضافية | References

- [Kubernetes Official Documentation](https://kubernetes.io/docs/)
- [Kubernetes API Reference](https://kubernetes.io/docs/reference/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

---

**آخر تحديث | Last Updated**: 2025
