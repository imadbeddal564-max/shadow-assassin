# 🏗️ هيكل الأكواد البرمجية
## Shadow Assassin - Code Structure

---

## 📁 تنظيم المجلدات

```
Assets/Scripts/
├── Player/                  # سكريبتات اللاعب
│   ├── PlayerController.cs
│   ├── PlayerStealthSystem.cs
│   ├── PlayerInventory.cs
│   └── PlayerStats.cs
│
├── Enemy/                   # سكريبتات الأعداء
│   ├── EnemyAI.cs
│   ├── EnemyVision.cs
│   ├── EnemyHearing.cs
│   └── EnemyController.cs
│
├── UI/                      # واجهات المستخدم
│   ├── HUDManager.cs
│   ├── MainMenuUI.cs
│   ├── PauseMenuUI.cs
│   └── ObjectiveUI.cs
│
├── Systems/                 # الأنظمة الرئيسية
│   ├── GameManager.cs
│   ├── AudioManager.cs
│   ├── SaveSystem.cs
│   ├── ObjectiveSystem.cs
│   └── MissionManager.cs
│
└── Utils/                   # فئات مساعدة
    ├── InputManager.cs
    ├── Singleton.cs
    ├── ObjectPool.cs
    └── Helpers.cs
```

---

## 📊 الفئات الرئيسية

### 1. Player System

#### PlayerController.cs
```csharp
// الدور:
- التحكم بحركة اللاعب
- إدارة الكاميرا
- معالجة المدخلات

// الدوال الرئيسية:
+ HandleInput()
+ HandleMovement()
+ HandleCamera()
+ GetCurrentSpeed()
+ IsCrouching()
```

#### PlayerStealthSystem.cs
```csharp
// الدور:
- حساب مستوى الكشف
- إدارة الضوضاء
- تأثيرات الإضاءة

// الدوال الرئيسية:
+ GetDetectionLevel()
+ GetNoiseLevel()
+ GetBrightness()
+ IsStealthy()
```

#### PlayerInventory.cs
```csharp
// الدور:
- إدارة الأدوات والأسلحة
- نظام الذخيرة

// البنية:
Item
├── name
├── type
└── quantity
```

#### PlayerStats.cs
```csharp
// الدور:
- إدارة الصحة
- الإحصائيات

// المتغيرات:
- currentHealth
- maxHealth
- ammo
```

---

### 2. Enemy System

#### EnemyAI.cs
```csharp
// الدور:
- إدارة منطق السلوك
- State Machine

// الحالات:
IDLE → PATROL → SUSPICIOUS 
  ↑                 ↓
  └←── SEARCH ← ALERT
        ↓
       COMBAT

// الدوال الرئيسية:
+ UpdateAIState()
+ ExecuteAIState()
+ ChangeState()
+ CanSeePlayer()
+ CanHearPlayer()
```

#### EnemyVision.cs
```csharp
// الدور:
- حساب الرؤية المتقدمة
- Raycasting

// الحسابات:
visibility = (distance/maxDist) × (brightness/maxBright)
           × (speed/maxSpeed) × fieldOfView
```

#### EnemyHearing.cs
```csharp
// الدور:
- استقبال أحداث الضوضاء
- حساب السمع

// المعادلة:
canHear = noiseLevel > threshold 
        && distance < hearingRange
```

---

### 3. UI System

#### HUDManager.cs
```csharp
// العناصر المعروضة:
- شريط الصحة ❤️
- مؤشر الكشف 👁️
- خريطة مصغرة 🗺️
- عرض الأهداف 🎯
- مستوى الضوضاء 🔊

// الدوال:
+ UpdateHealthBar()
+ UpdateDetectionIndicator()
+ ShowObjective()
+ UpdateMinimap()
```

#### MainMenuUI.cs
```csharp
// الأزرار:
- جديد
- تحميل
- الإعدادات
- خروج

// الدوال:
+ NewGame()
+ LoadGame()
+ QuitGame()
```

---

### 4. Game Systems

#### GameManager.cs
```csharp
// الدور:
- الإدارة المركزية
- Singleton Pattern

// الدوال:
+ Start/PauseGame()
+ LoadLevel()
+ GameOver()
+ GetCurrentLevel()
```

#### AudioManager.cs
```csharp
// الدور:
- إدارة الأصوات
- التأثيرات الصوتية

// الدوال:
+ PlaySFX(clip)
+ PlayMusic(clip)
+ StopMusic()
+ SetVolume(level)
```

#### SaveSystem.cs
```csharp
// الدور:
- حفظ البيانات
- تحميل البيانات

// البيانات المحفوظة:
{
  playerPos,
  health,
  inventory,
  levelProgress,
  timestamp
}
```

#### ObjectiveSystem.cs
```csharp
// الدور:
- إدارة الأهداف
- تتبع التقدم

// الأنواع:
- PRIMARY: يجب تحقيقها
- SECONDARY: اختيارية
- HIDDEN: مخفية
```

---

## 🔄 سير البيانات (Data Flow)

```
INPUT (Keyboard/Mouse)
  ↓
PlayerController
  ├→ PlayerStealthSystem
  │   ├→ Detection Level
  │   ├→ Noise Level
  │   └→ Brightness
  │
  └→ Movement Update
     ├→ HUDManager (تحديث العرض)
     └→ EnemyAI (يسمع/يرى)
     
EnemyAI
  ├→ State Change
  ├→ Chase Event
  └→ AudioManager (الإنذار)
  
GameManager
  ├→ Check Win/Lose
  └→ UI Update
```

---

## 🎮 دورة الألعاب (Game Loop)

```
1️⃣ INPUT PHASE
   ├─ اقرأ المدخلات
   └─ قم باستدعاء الأحداث
   
2️⃣ UPDATE PHASE
   ├─ حدّث حركة اللاعب
   ├─ حدّث AI الأعداء
   └─ حدّث الأنظمة
   
3️⃣ PHYSICS PHASE
   ├─ معالجة التصادمات
   └─ تطبيق القوى
   
4️⃣ RENDER PHASE
   ├─ حدّث الكاميرا
   ├─ رسم المشهد
   └─ حدّث الواجهات
```

---

## 🎯 الأحداث (Events System)

### أحداث اللاعب
```csharp
// حركة
PlayerController.MovementChanged += OnMovementChanged;

// تسلل
PlayerStealthSystem.DetectionChanged += OnDetectionChanged;
PlayerStealthSystem.NoiseGenerated += OnNoiseGenerated;

// الصحة
PlayerStats.HealthChanged += OnHealthChanged;
PlayerStats.PlayerDied += OnPlayerDied;
```

### أحداث الأعداء
```csharp
// الحالة
EnemyAI.StateChanged += OnEnemyStateChanged;

// الكشف
EnemyAI.PlayerDetected += OnPlayerDetected;
EnemyAI.PlayerSeen += OnPlayerSeen;
```

### ��حداث النظام
```csharp
// اللعبة
GameManager.GamePaused += OnGamePaused;
GameManager.LevelCompleted += OnLevelCompleted;
GameManager.GameOver += OnGameOver;

// الأهداف
ObjectiveSystem.ObjectiveUpdated += OnObjectiveUpdated;
ObjectiveSystem.ObjectiveCompleted += OnObjectiveCompleted;
```

---

## 💾 نمط حفظ البيانات

```json
{
  "version": "1.0",
  "timestamp": "2026-05-19 10:30:00",
  "level": 1,
  "player": {
    "position": [10.5, 0, -5.3],
    "health": 85,
    "inventory": [
      {"name": "Knife", "quantity": 1},
      {"name": "Ammo", "quantity": 30}
    ]
  },
  "objectives": [
    {"id": 1, "completed": true},
    {"id": 2, "completed": false}
  ],
  "enemies": [
    {"id": 1, "position": [5, 0, 0], "state": "Patrol"}
  ]
}
```

---

## ⚡ نمط Singleton

```csharp
public class GameManager : Singleton<GameManager>
{
    public override void Initialize()
    {
        // التهيئة
    }
}

// الاستخدام:
GameManager.Instance.StartGame();
```

---

## 🎓 أمثلة الاستخدام

### مثال 1: الاستماع لحدث الكشف
```csharp
void Start()
{
    PlayerStealthSystem.DetectionChanged += HandleDetectionChanged;
}

void HandleDetectionChanged(float detectionLevel)
{
    Debug.Log($"مستوى الكشف: {detectionLevel}");
}
```

### مثال 2: تنفيذ كود بناءً على حالة العدو
```csharp
if (enemyAI.GetCurrentState() == EnemyAI.AIState.Alert)
{
    PlayAlarmSound();
}
```

### مثال 3: حفظ وتحميل اللعبة
```csharp
// حفظ
SaveSystem.SaveGame("save_slot_1");

// تحميل
SaveSystem.LoadGame("save_slot_1");
```

---

**آخر تحديث:** مايو 2026
