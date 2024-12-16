# المكتبات المستخدمة في المشروع

هذا الدليل يشرح جميع المكتبات المستخدمة في المشروع وأفضل الممارسات لاستخدامها.

## 📱 1. مكتبات واجهة المستخدم

### Fluent UI (^4.9.2)
- **الغرض**: تطبيق نمط تصميم Windows الحديث
- **المميزات**:
  - مكونات واجهة مستخدم جاهزة بنمط Windows
  - تناسق كامل مع نظام التشغيل
  - دعم للإيماءات واللمس
- **التكامل مع المكتبات الأخرى**:
  - يعمل مع SystemTheme للتكيف مع سمة النظام
  - يدعم ScreenUtil للتكيف مع أحجام الشاشات
  - يتكامل مع GetX للتنقل وإدارة الحالة
- **مثال عملي**:
  ```dart
  class MyApp extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
      return FluentApp(
        title: 'Flutter Publisher',
        theme: FluentThemeData(
          accentColor: Colors.blue,
          brightness: Brightness.light,
          visualDensity: VisualDensity.standard,
          typography: Typography.standard,
        ),
        home: NavigationView(
          pane: NavigationPane(
            selected: _currentIndex,
            items: [
              PaneItem(
                icon: Icon(FluentIcons.home),
                title: Text('الرئيسية'),
              ),
              PaneItem(
                icon: Icon(FluentIcons.settings),
                title: Text('الإعدادات'),
              ),
            ],
          ),
          content: NavigationBody(
            index: _currentIndex,
            children: [
              HomePage(),
              SettingsPage(),
            ],
          ),
        ),
      );
    }
  }
  ```

### Window Manager (^0.3.9)
- **الغرض**: إدارة نافذة التطبيق بشكل احترافي
- **المميزات**:
  - تحكم كامل في حجم وموقع النافذة
  - دعم للتحريك السلس
  - تخصيص شريط العنوان
- **التكامل**:
  - يعمل مع FluentUI لتجربة Windows كاملة
  - يدعم SystemTheme لتناسق شريط العنوان
- **مثال عملي**:
  ```dart
  void main() async {
    WidgetsFlutterBinding.ensureInitialized();
    await windowManager.ensureInitialized();

    WindowOptions windowOptions = WindowOptions(
      size: Size(1280, 720),
      center: true,
      title: 'Flutter Publisher',
      minimumSize: Size(800, 600),
      backgroundColor: Colors.transparent,
      skipTaskbar: false,
      titleBarStyle: TitleBarStyle.hidden,
    );

    await windowManager.waitUntilReadyToShow(windowOptions, () async {
      await windowManager.show();
      await windowManager.focus();
    });

    runApp(MyApp());
  }
  ```

### System Theme (^2.3.1)
- **الغرض**: تكامل مع سمة نظام التشغيل
- **المميزات**:
  - تبديل تلقائي بين الوضع الفاتح والداكن
  - استخدام ألوان النظام
  - دعم خاص لـ FluentUI
- **التكامل**:
  - يعمل مع FluentUI للتناسق الكامل
  - يدعم WindowManager لشريط العنوان
- **مثال عملي**:
  ```dart
  class MyApp extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
      return FluentApp(
        theme: FluentThemeData(
          brightness: SystemTheme.isDarkMode 
            ? Brightness.dark 
            : Brightness.light,
          accentColor: SystemTheme.accentColor.toAccentColor(),
        ),
        // ...
      );
    }
  }
  ```

### Flutter ScreenUtil (^5.9.0)
- **الغرض**: تكيف التطبيق مع مختلف أحجام الشاشات
- **المميزات**:
  - حسابات تلقائية للأحجام
  - دعم للشاشات المختلفة
  - سهولة الاستخدام
- **التكامل**:
  - يعمل مع FluentUI لتناسق المكونات
  - يدعم WindowManager للتكيف مع حجم النافذة
- **مثال عملي**:
  ```dart
  void main() {
    runApp(
      ScreenUtilInit(
        designSize: Size(1280, 720),
        builder: (context, child) => MyApp(),
      ),
    );
  }

  // استخدام في الواجهة
  Container(
    width: 200.w,    // عرض متكيف
    height: 100.h,   // ارتفاع متكيف
    padding: EdgeInsets.all(10.r),  // هوامش متكيفة
    child: Text(
      'مثال',
      style: TextStyle(fontSize: 16.sp),  // حجم خط متكيف
    ),
  )
  ```

## 💾 2. إدارة الحالة والبيانات

### GetX (^4.6.6)
- **الغرض**: إدارة الحالة والتنقل بكفاءة عالية
- **المميزات**:
  - إدارة حالة خفيفة وفعالة
  - نظام تنقل متقدم
  - حقن التبعيات المدمج
  - إدارة الموارد والذاكرة
- **التكامل**:
  - يعمل مع FluentUI للتنقل والحالة
  - يتكامل مع GetStorage للتخزين المحلي
  - يدعم Dio للطلبات الشبكية
- **أمثلة عملية**:

  1. **إدارة الحالة**:
  ```dart
  // نموذج البيانات
  class Project {
    final String name;
    final String path;
    
    Project({required this.name, required this.path});
  }

  // المتحكم
  class ProjectController extends GetxController {
    final projects = <Project>[].obs;
    final isLoading = false.obs;

    Future<void> loadProjects() async {
      try {
        isLoading.value = true;
        // تحميل المشاريع
        final List<Project> loadedProjects = await ProjectService.getProjects();
        projects.value = loadedProjects;
      } finally {
        isLoading.value = false;
      }
    }

    void addProject(Project project) {
      projects.add(project);
    }
  }

  // استخدام في الواجهة
  class ProjectsView extends GetView<ProjectController> {
    @override
    Widget build(BuildContext context) {
      return ScaffoldPage(
        content: Obx(() => controller.isLoading.value
          ? const Center(child: ProgressRing())
          : ListView.builder(
              itemCount: controller.projects.length,
              itemBuilder: (context, index) {
                final project = controller.projects[index];
                return ListTile(
                  title: Text(project.name),
                  subtitle: Text(project.path),
                );
              },
            ),
        ),
      );
    }
  }
  ```

  2. **التنقل والتبعيات**:
  ```dart
  void main() {
    // تسجيل التبعيات
    Get.put(ProjectController());
    Get.put(SettingsController());
    Get.put(ThemeController());

    runApp(
      GetMaterialApp(
        initialRoute: '/home',
        getPages: [
          GetPage(
            name: '/home',
            page: () => HomePage(),
            binding: HomeBinding(),
          ),
          GetPage(
            name: '/settings',
            page: () => SettingsPage(),
            binding: SettingsBinding(),
          ),
        ],
      ),
    );
  }

  // التنقل
  void navigateToSettings() {
    Get.toNamed('/settings', arguments: {'theme': 'dark'});
  }
  ```

### Get Storage (^2.1.1)
- **الغرض**: تخزين البيانات محلياً بكفاءة
- **المميزات**:
  - تخزين سريع وخفيف
  - دعم للتشفير
  - مزامنة تلقائية
- **التكامل**:
  - يعمل مع GetX للحالة المستمرة
  - يدعم تخزين إعدادات التطبيق
- **أمثلة عملية**:

  1. **تخزين الإعدادات**:
  ```dart
  class SettingsService {
    final storage = GetStorage('settings');

    Future<void> saveTheme(String theme) async {
      await storage.write('theme', theme);
    }

    String? getTheme() {
      return storage.read('theme');
    }

    Future<void> saveWindowSize(Size size) async {
      await storage.write('window_size', {
        'width': size.width,
        'height': size.height,
      });
    }

    Size? getWindowSize() {
      final Map<String, dynamic>? size = storage.read('window_size');
      if (size != null) {
        return Size(size['width'], size['height']);
      }
      return null;
    }
  }
  ```

  2. **استخدام مع GetX**:
  ```dart
  class ThemeController extends GetxController {
    final _settingsService = Get.find<SettingsService>();
    final theme = 'system'.obs;

    @override
    void onInit() {
      super.onInit();
      // تحميل السمة المحفوظة
      final savedTheme = _settingsService.getTheme();
      if (savedTheme != null) {
        theme.value = savedTheme;
      }
    }

    Future<void> changeTheme(String newTheme) async {
      theme.value = newTheme;
      await _settingsService.saveTheme(newTheme);
    }
  }
  ```

### Firedart (^0.9.7)
- **الغرض**: التعامل مع Firebase في تطبيقات سطح المكتب
- **المميزات**:
  - دعم المصادقة
  - قاعدة البيانات في الوقت الفعلي
  - تخزين الملفات
- **التكامل**:
  - يعمل مع GetX للحالة
  - يتكامل مع GetStorage للتخزين المؤقت
- **أمثلة عملية**:

  1. **المصادقة**:
  ```dart
  class AuthService {
    final _auth = FirebaseAuth.instance;
    
    Future<User?> signIn(String email, String password) async {
      try {
        final user = await _auth.signIn(email, password);
        return user;
      } catch (e) {
        Get.snackbar('خطأ', 'فشل تسجيل الدخول');
        return null;
      }
    }

    Future<void> signOut() async {
      await _auth.signOut();
    }
  }
  ```

  2. **قاعدة البيانات**:
  ```dart
  class ProjectRepository {
    final _firestore = Firestore.instance;
    
    Future<List<Project>> getProjects() async {
      final snapshot = await _firestore
          .collection('projects')
          .get();
          
      return snapshot.map((doc) => Project(
        name: doc.map['name'],
        path: doc.map['path'],
      )).toList();
    }

    Future<void> addProject(Project project) async {
      await _firestore.collection('projects').add({
        'name': project.name,
        'path': project.path,
        'createdAt': DateTime.now(),
      });
    }
  }
  ```

## 🔌 3. خدمات وتكامل

### GitHub (^9.22.0)
- **الغرض**: تكامل مع GitHub API
- **المميزات**:
  - إدارة المستودعات
  - التعامل مع الإصدارات
  - إدارة العلامات والفروع
- **التكامل**:
  - يعمل مع GetX للحالة
  - يتكامل مع Dio للطلبات
- **أمثلة عملية**:

  1. **إدارة المستودعات**:
  ```dart
  class GitHubService {
    final github = GitHub(auth: Authentication.withToken('YOUR_TOKEN'));
    
    Future<List<Repository>> getUserRepos() async {
      try {
        final repos = await github.repositories.listUserRepositories().toList();
        return repos;
      } catch (e) {
        Get.snackbar('خطأ', 'فشل تحميل المستودعات');
        return [];
      }
    }

    Future<Release?> createRelease(
      String owner,
      String repo,
      String tag,
      String name,
      String body,
    ) async {
      try {
        final release = await github.repositories.createRelease(
          RepositorySlug(owner, repo),
          CreateRelease.from(
            tagName: tag,
            name: name,
            body: body,
          ),
        );
        return release;
      } catch (e) {
        Get.snackbar('خطأ', 'فشل إنشاء الإصدار');
        return null;
      }
    }
  }
  ```

  2. **إدارة الإصدارات**:
  ```dart
  class ReleaseController extends GetxController {
    final _githubService = Get.find<GitHubService>();
    final releases = <Release>[].obs;
    final isLoading = false.obs;

    Future<void> loadReleases(String owner, String repo) async {
      try {
        isLoading.value = true;
        final repository = await _githubService.github.repositories
            .getRepository(RepositorySlug(owner, repo));
            
        final releasesList = await _githubService.github.repositories
            .listReleases(repository.slug)
            .toList();
            
        releases.value = releasesList;
      } finally {
        isLoading.value = false;
      }
    }

    Future<void> publishRelease(
      String owner,
      String repo,
      String version,
      String notes,
    ) async {
      final release = await _githubService.createRelease(
        owner,
        repo,
        'v$version',
        'Version $version',
        notes,
      );

      if (release != null) {
        releases.insert(0, release);
      }
    }
  }
  ```

### Dio (^5.4.0)
- **الغرض**: مكتبة متقدمة للطلبات الشبكية
- **المميزات**:
  - دعم REST API
  - إدارة الطلبات المتزامنة
  - معالجة الأخطاء المتقدمة
- **التكامل**:
  - يعمل مع GetX للحالة
  - يتكامل مع GitHub API
- **أمثلة عملية**:

  1. **إعداد وتكوين Dio**:
  ```dart
  class ApiService extends GetxService {
    late final Dio _dio;
    
    @override
    void onInit() {
      super.onInit();
      _dio = Dio(BaseOptions(
        baseUrl: 'https://api.example.com',
        connectTimeout: Duration(seconds: 5),
        receiveTimeout: Duration(seconds: 3),
        headers: {'Accept': 'application/json'},
      ));

      _dio.interceptors.add(InterceptorsWrapper(
        onRequest: (options, handler) {
          // إضافة رمز المصادقة
          final token = GetStorage().read('token');
          if (token != null) {
            options.headers['Authorization'] = 'Bearer $token';
          }
          return handler.next(options);
        },
        onError: (error, handler) {
          // معالجة الأخطاء
          if (error.response?.statusCode == 401) {
            // تسجيل الخروج عند انتهاء صلاحية الرمز
            Get.find<AuthService>().signOut();
          }
          return handler.next(error);
        },
      ));
    }

    Future<T?> get<T>(
      String path, {
      Map<String, dynamic>? queryParameters,
      Options? options,
    }) async {
      try {
        final response = await _dio.get<T>(
          path,
          queryParameters: queryParameters,
          options: options,
        );
        return response.data;
      } catch (e) {
        _handleError(e);
        return null;
      }
    }

    void _handleError(dynamic error) {
      if (error is DioException) {
        Get.snackbar(
          'خطأ',
          error.response?.data?['message'] ?? 'حدث خطأ في الاتصال',
          snackPosition: SnackPosition.BOTTOM,
        );
      }
    }
  }
  ```

  2. **استخدام مع GetX**:
  ```dart
  class ApiController extends GetxController {
    final _apiService = Get.find<ApiService>();
    final data = Rx<Map<String, dynamic>?>(null);
    final isLoading = false.obs;

    Future<void> fetchData() async {
      try {
        isLoading.value = true;
        final response = await _apiService.get<Map<String, dynamic>>('/data');
        data.value = response;
      } finally {
        isLoading.value = false;
      }
    }
  }
  ```

### Upgrader (^8.4.0)
- **الغرض**: تحديث تلقائي للتطبيق
- **المميزات**:
  - فحص التحديثات
  - تنزيل وتثبيت تلقائي
  - تخصيص رسائل التحديث
- **التكامل**:
  - يعمل مع GetX للحالة
  - يتكامل مع GitHub للإصدارات
- **أمثلة عملية**:

  1. **إعداد التحديث التلقائي**:
  ```dart
  class UpdateService extends GetxService {
    late final Upgrader upgrader;
    
    @override
    void onInit() {
      super.onInit();
      upgrader = Upgrader(
        durationUntilAlertAgain: const Duration(days: 1),
        dialogStyle: UpgradeDialogStyle.cupertino,
        messages: UpgraderMessages(code: 'ar'),
        onUpdate: () {
          // تنفيذ التحديث
          return true;
        },
      );
    }

    Widget wrapWithUpgrader(Widget child) {
      return UpgradeAlert(
        upgrader: upgrader,
        child: child,
      );
    }
  }
  ```

  2. **تكامل مع GitHub**:
  ```dart
  class GithubUpgrader extends Upgrader {
    final GitHubService _githubService;
    
    GithubUpgrader(this._githubService) {
      minAppVersion = '1.0.0';
      debugLogging = true;
    }

    @override
    Future<bool> initialize() async {
      try {
        final releases = await _githubService.github.repositories
            .listReleases(RepositorySlug('owner', 'repo'))
            .toList();
            
        if (releases.isNotEmpty) {
          final latestRelease = releases.first;
          final latestVersion = latestRelease.tagName;
          
          if (latestVersion != null) {
            // مقارنة الإصدار الحالي مع أحدث إصدار
            final currentVersion = await currentInstalledVersion();
            if (currentVersion != null) {
              final shouldUpgrade = shouldUpdate(
                currentVersion,
                latestVersion.replaceAll('v', ''),
              );
              
              if (shouldUpgrade) {
                Get.snackbar(
                  'تحديث متوفر',
                  'يتوفر إصدار جديد: $latestVersion',
                  duration: Duration(seconds: 5),
                  mainButton: TextButton(
                    onPressed: () => _startUpdate(latestRelease),
                    child: Text('تحديث'),
                  ),
                );
              }
            }
          }
        }
        return true;
      } catch (e) {
        return false;
      }
    }

    Future<void> _startUpdate(Release release) async {
      // تنفيذ عملية التحديث
    }
  }
  ```