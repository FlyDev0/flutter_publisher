# الهيكل البرمجي للتطبيق

## 1. المكونات الأساسية (Core)

### 1.1 الثوابت (Constants)
```dart
lib/core/constants/
├── app_constants.dart      # ثوابت التطبيق العامة
├── api_constants.dart      # ثوابت API
├── assets_constants.dart   # مسارات الأصول
├── theme_constants.dart    # ثوابت التنسيق
└── routes_constants.dart   # مسارات التنقل (GetX)
```

### 1.2 السمات (Theme)
```dart
lib/core/theme/
├── app_theme.dart         # تكامل FluentUI مع SystemTheme
├── color_schemes.dart     # مخططات الألوان
├── text_styles.dart       # أنماط النصوص مع GoogleFonts
└── dimensions.dart        # الأبعاد مع ScreenUtil
```

### 1.3 الأدوات المساعدة (Utils)
```dart
lib/core/utils/
├── extensions/            # امتدادات Dart
├── helpers/              # دوال مساعدة
├── storage/              # تهيئة GetStorage
└── window/              # إدارة WindowManager
```

## 2. طبقة البيانات (Data Layer)

### 2.1 النماذج (Models)
```dart
lib/data/models/
├── project/
│   ├── project_model.dart     # نموذج المشروع
│   ├── build_config.dart      # إعدادات البناء
│   └── publish_config.dart    # إعدادات النشر
├── auth/
│   ├── user_model.dart        # نموذج المستخدم (Firedart)
│   └── credentials.dart       # بيانات الاعتماد
└── analytics/
    ├── build_stats.dart       # إحصائيات البناء
    └── publish_stats.dart     # إحصائيات النشر
```

### 2.2 المستودعات (Repositories)
```dart
lib/data/repositories/
├── project_repository.dart    # التعامل مع المشاريع (GitHub)
├── auth_repository.dart       # المصادقة (Firedart)
├── build_repository.dart      # عمليات البناء
└── analytics_repository.dart  # التحليلات
```

### 2.3 الخدمات (Services)
```dart
lib/data/services/
├── github_service.dart        # خدمات GitHub
├── firebase_service.dart      # خدمات Firedart
├── storage_service.dart       # GetStorage
├── http_service.dart          # Dio للاتصالات
└── analytics_service.dart     # خدمات التحليلات
```

## 3. الميزات (Features)

### 3.1 المصادقة (Auth)
```dart
lib/features/auth/
├── presentation/
│   ├── screens/              # شاشات FluentUI
│   └── widgets/              # مكونات مخصصة
├── domain/
│   └── auth_controller.dart  # متحكم GetX
└── data/
    └── auth_provider.dart    # مزود Firedart
```

### 3.2 إدارة المشاريع (Projects)
```dart
lib/features/projects/
├── presentation/            # واجهة FluentUI
├── domain/                  # متحكمات GetX
└── data/                    # تكامل مع GitHub
```

### 3.3 البناء (Builder)
```dart
lib/features/builder/
├── presentation/           # واجهة FluentUI
├── domain/                 # متحكمات GetX
└── data/                   # خدمات البناء
```

### 3.4 النشر (Publisher)
```dart
lib/features/publisher/
├── presentation/          # واجهة FluentUI
├── domain/               # متحكمات GetX
└── data/                # خدمات النشر
```

## 4. العرض (Presentation)

### 4.1 الشاشات الرئيسية (Screens)
```dart
lib/presentation/screens/
├── main/                 # شاشات FluentUI
├── common/              # مكونات مشتركة
└── shared/             # تخطيطات مشتركة
```

### 4.2 المكونات المشتركة (Widgets)
```dart
lib/presentation/widgets/
├── common/             # مكونات FluentUI
├── forms/             # نماذج الإدخال
└── dialogs/           # مربعات الحوار
```

### 4.3 المتحكمات (Controllers)
```dart
lib/presentation/controllers/
├── navigation_controller.dart  # تنقل GetX
├── theme_controller.dart       # تحكم SystemTheme
└── app_controller.dart         # تحكم عام
```

## 5. التكامل (Integration)

### 5.1 الخدمات الخارجية
```dart
lib/integration/
├── github/                    # تكامل GitHub
├── firebase/                  # تكامل Firedart
└── stores/                    # متاجر التطبيقات
```

## نمط التصميم والتقنيات

1. **إدارة الحالة**:
   - GetX للتحكم والتنقل
   - GetStorage للتخزين المحلي
   - Reactive Programming

2. **واجهة المستخدم**:
   - FluentUI للتصميم
   - SystemTheme للسمات
   - ScreenUtil للتكيف
   - WindowManager للنوافذ

3. **الخدمات الخلفية**:
   - Firedart للتخزين السحابي
   - GitHub API للمستودعات
   - Dio للاتصالات

4. **التحديث والتوزيع**:
   - Upgrader للتحديثات
   - متاجر التطبيقات للنشر
