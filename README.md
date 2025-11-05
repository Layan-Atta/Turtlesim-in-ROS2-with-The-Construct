# Turtlesim-in-ROS2-with-The-Construct
# تشغيل Turtlesim في ROS2 🐢

## نظرة عامة
هذا المشروع يوضح كيفية تشغيل واستخدام حزمة **Turtlesim** في ROS2 باستخدام منصة **The Construct**. تم تنفيذ جميع خطوات الـ [Tutorial الرسمي](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Introducing-Turtlesim/Introducing-Turtlesim.html) من ROS2 Documentation.

## البيئة المستخدمة
- **المنصة**: The Construct (https://www.theconstructsim.com)
- **ROS2 Distribution**: Humble
- **الحزمة**: turtlesim
- **الأدوات**: ros2 CLI, rqt

---

## 📚 الخطوات المنفذة

### 1. تشغيل Turtlesim Node

في Terminal الأول:
```bash
ros2 run turtlesim turtlesim_node
```

**النتيجة:**
- ✅ نافذة زرقاء تفتح مع سلحفاة في المنتصف (turtle1)
- ✅ رسالة تظهر في Terminal: `[INFO] [turtlesim]: Starting turtlesim with node name /turtlesim`

---

### 2. التحكم بالسلحفاة (Teleoperation)

في Terminal الثاني:
```bash
ros2 run turtlesim turtle_teleop_key
```

**الاستخدام:**
- استخدم **أزرار الأسهم** (↑ ↓ ← →) لتحريك السلحفاة
- السلحفاة ترسم خط خلفها أثناء الحركة
- اضغط `Q` أو `Ctrl+C` للإيقاف

---

### 3. استكشاف ROS2 Commands

#### عرض Nodes النشطة:
```bash
ros2 node list
```
**النتيجة:**
```
/turtlesim
/teleop_turtle
```

#### عرض Topics:
```bash
ros2 topic list
```
**النتيجة:**
```
/turtle1/cmd_vel
/turtle1/color_sensor
/turtle1/pose
```

#### عرض Services:
```bash
ros2 service list
```
**النتيجة:**
```
/spawn
/turtle1/set_pen
/turtle1/teleport_absolute
/reset
/clear
```

---

### 4. استخدام rqt (الواجهة الرسومية)

#### تشغيل rqt:
```bash
rqt
```

ثم: **Plugins → Services → Service Caller**

---

### 5. إنشاء سلحفاة ثانية (Spawn)

#### باستخدام rqt Service Caller:

1. اختر Service: `/spawn`
2. املأ القيم:
   ```
   x: 1.0
   y: 1.0
   theta: 0.0
   name: "turtle2"
   ```
3. اضغط **Call**

**النتيجة:**
- ✅ سلحفاة جديدة (turtle2) تظهر في الموقع (1.0, 1.0)
- ✅ Response في rqt: `name: "turtle2"`

#### باستخدام Terminal:
```bash
ros2 service call /spawn turtlesim/srv/Spawn "{x: 2.0, y: 2.0, theta: 0.0, name: 'turtle2'}"
```

![rqt spawn](screenshots/rqt_spawn.png)

---

### 6. تغيير لون ونمط القلم (Set Pen)

#### باستخدام rqt Service Caller:

1. اختر Service: `/turtle1/set_pen`
2. املأ القيم:
   ```
   r: 255       (أحمر)
   g: 0         (أخضر)
   b: 0         (أزرق)
   width: 5     (سُمك الخط)
   off: 0       (القلم مُفعّل)
   ```
3. اضغط **Call**

**النتيجة:**
- ✅ turtle1 الآن ترسم بخط **أحمر سميك**! 🔴

![set pen](screenshots/set_pen.png)

#### باستخدام Terminal:
```bash
ros2 service call /turtle1/set_pen turtlesim/srv/SetPen "{r: 255, g: 0, b: 0, width: 5, off: 0}"
```

---

### 7. Remapping (التحكم بالسلحفاة الثانية)

لإنشاء تحكم مستقل لـ turtle2:

```bash
ros2 run turtlesim turtle_teleop_key --ros-args --remap turtle1/cmd_vel:=turtle2/cmd_vel
```

**الآن لديك:**
- Terminal 1 → يتحكم بـ turtle1 (خط أحمر 🔴)
- Terminal 2 → يتحكم بـ turtle2 (خط أزرق 🔵)

![remapping](screenshots/remapping.png)

---

## 🎨 خدمات إضافية مفيدة

### مسح الخطوط المرسومة:
```bash
ros2 service call /clear std_srvs/srv/Empty
```

### إعادة تعيين النافذة (Reset):
```bash
ros2 service call /reset std_srvs/srv/Empty
```
**ملاحظة:** هذا يحذف جميع السلاحف ما عدا turtle1 ويعيدها للمركز

### إخفاء/إظهار القلم:
```bash
# إيقاف الرسم
ros2 service call /turtle1/set_pen turtlesim/srv/SetPen "{r: 255, g: 0, b: 0, width: 5, off: 1}"

# تفعيل الرسم
ros2 service call /turtle1/set_pen turtlesim/srv/SetPen "{r: 255, g: 0, b: 0, width: 5, off: 0}"
```

---

## 🔧 أوامر متقدمة

### التحكم المباشر بالسلحفاة (بدون لوحة المفاتيح):

#### تحريك للأمام:
```bash
ros2 topic pub --once /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```

#### رسم دائرة:
```bash
ros2 topic pub --rate 1 /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"
```
*اضغط `Ctrl+C` لإيقاف الدائرة*

## 📊 مراقبة البيانات

### عرض موقع السلحفاة المباشر:
```bash
ros2 topic echo /turtle1/pose
```

**النتيجة:**
```
x: 5.544445
y: 5.544445
theta: 0.0
linear_velocity: 0.0
angular_velocity: 0.0
```

### عرض معلومات Topic:
```bash
ros2 topic info /turtle1/cmd_vel
```

### عرض بنية الرسالة:
```bash
ros2 interface show geometry_msgs/msg/Twist
```

---

## 📸 لقطات الشاشة

### النافذة الأساسية مع سلحفاتين
![Two Turtles](screenshots/two_turtles.png)
*turtle1 (برتقالية) و turtle2 (خضراء)*

### rqt Service Caller - Spawn
![rqt Spawn](screenshots/rqt_spawn_success.png)
*إنشاء turtle2 بنجاح*

### تغيير لون القلم
![Red Pen](screenshots/red_pen_circle.png)
*turtle1 ترسم دائرة حمراء سميكة*

### Terminals النشطة
![Terminals](screenshots/terminals_setup.png)
*تعدد Terminals للتحكم المستقل*

---

## 🎯 النتائج المحققة

✅ تشغيل turtlesim_node بنجاح  
✅ التحكم بالسلحفاة باستخدام لوحة المفاتيح  
✅ استخدام rqt للواجهة الرسومية  
✅ إنشاء سلحفاة ثانية (spawn)  
✅ تخصيص لون وسُمك القلم  
✅ التحكم المستقل بسلحفاتين (remapping)  
✅ استكشاف Nodes, Topics, و Services  

---

## 🚀 المفاهيم المستفادة

### 1. **Nodes (العُقد)**
- كل برنامج في ROS2 هو node
- مثال: `turtlesim_node`, `teleop_turtle`

### 2. **Topics (المواضيع)**
- قنوات اتصال بين الـ nodes
- مثال: `/turtle1/cmd_vel` يحمل أوامر الحركة

### 3. **Services (الخدمات)**
- طلبات/استجابات فورية
- مثال: `/spawn` لإنشاء سلحفاة

### 4. **Messages (الرسائل)**
- بنية البيانات المرسلة في Topics
- مثال: `geometry_msgs/msg/Twist` للحركة

### 5. **Remapping (إعادة التوجيه)**
- تغيير مسار Topics لأهداف مختلفة
- مثال: تحويل أوامر turtle1 إلى turtle2

---

## 📖 المراجع

- [ROS2 Humble Documentation](https://docs.ros.org/en/humble/)
- [Turtlesim Tutorial](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Introducing-Turtlesim/Introducing-Turtlesim.html)
- [The Construct](https://www.theconstructsim.com)
- [ROS2 CLI Tools](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools.html)

---

## 🛠️ استكشاف الأخطاء

### المشكلة: السلحفاة لا تتحرك
**الحل:** تأكد أن Terminal الخاص بـ `turtle_teleop_key` نشط (اضغط عليه بالماوس قبل استخدام الأسهم)

### المشكلة: rqt لا يعرض Plugins
**الحل:** أغلق rqt واستخدم:
```bash
rqt --force-discover
```

### المشكلة: خطأ "turtle already exists"
**الحل:** استخدم اسم مختلف أو نفذ `/reset` أولاً

### المشكلة: نافذتين turtlesim مفتوحتين
**الحل:** أغلق الزائدة وتأكد من `ros2 node list` يعرض node واحد فقط

---

## 👨‍💻 المؤلف

تم إنجاز هذا المشروع كجزء من تعلم أساسيات ROS2 باستخدام منصة The Construct.

**التاريخ:** نوفمبر 2024  
**ROS2 Version:** Humble Hawksbill

---

## 📜 الرخصة

هذا المشروع تعليمي ويتبع [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0) (نفس رخصة ROS2).

---

## ⭐ ملاحظات إضافية

- جميع الأوامر تم اختبارها على **ROS2 Humble**
- يمكن تطبيق نفس الخطوات على التوزيعات الأخرى (Foxy, Iron, etc.)
- The Construct توفر بيئة مثالية للتعلم بدون تثبيت محلي
- ينصح بالتجربة والاستكشاف لفهم أعمق للمفاهيم

---

## 🎓 الخطوات التالية

بعد إتمام هذا Tutorial، يُنصح بالانتقال إلى:
1. [Understanding ROS2 Nodes](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Nodes/Understanding-ROS2-Nodes.html)
2. [Understanding ROS2 Topics](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Topics/Understanding-ROS2-Topics.html)
3. [Understanding ROS2 Services](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Understanding-ROS2-Services/Understanding-ROS2-Services.html)

---

**Happy Learning! 🚀🐢**
