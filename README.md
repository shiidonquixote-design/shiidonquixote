globalsummary/              # Root folder
 ├─ lib/                    # Folder kode Flutter
 │   ├─ main.dart
 │   ├─ pages/
 │   │   ├─ home_page.dart
 │   │   └─ news_detail_page.dart
 │   └─ widgets/
 │       └─ news_card.dart
 ├─ android/                 # placeholder
 ├─ ios/                     # placeholder
 ├─ pubspec.yaml
 └─ README.md
 pubspec.yaml
 name: globalsummary
description: Global Summary - Real-time global news app
publish_to: 'none'
version: 1.0.0+1

environment:
  sdk: ">=3.0.0 <4.0.0"

dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.12.0
  firebase_messaging: ^14.7.0
  http: ^1.1.0
  provider: ^6.0.5

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^2.0.0
  lib/main.dart
  import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'package:firebase_messaging/firebase_messaging.dart';
import 'pages/home_page.dart';

Future<void> _firebaseMessagingBackgroundHandler(RemoteMessage message) async {
  await Firebase.initializeApp();
}

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
  FirebaseMessaging.onBackgroundMessage(_firebaseMessagingBackgroundHandler);
  runApp(const GlobalSummaryApp());
}

class GlobalSummaryApp extends StatelessWidget {
  const GlobalSummaryApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Global Summary',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(useMaterial3: true, colorSchemeSeed: Colors.blue),
      home: const HomePage(),
    );
  }
}
lib/pages/home_page.dart
import 'package:flutter/material.dart';
import 'news_detail_page.dart';
import 'package:firebase_messaging/firebase_messaging.dart';

class HomePage extends StatefulWidget {
  const HomePage({super.key});
  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  void initState() {
    super.initState();
    FirebaseMessaging.instance.subscribeToTopic('global_news');
    FirebaseMessaging.onMessage.listen((message) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text(message.notification?.title ?? 'Breaking News')),
      );
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Global Summary')),
      body: ListView.builder(
        itemCount: 10,
        itemBuilder: (context, index) {
          return ListTile(
            title: Text('Global News Headline ${index + 1}'),
            subtitle: const Text('AI summary of global news...'),
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (_) => NewsDetailPage(index: index),
                ),
              );
            },
          );
        },
      ),
    );
  }
}
lib/pages/news_detail_page.dart
import 'package:flutter/material.dart';

class NewsDetailPage extends StatelessWidget {
  final int index;
  const NewsDetailPage({super.key, required this.index});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Detail News')),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: const [
            Text('Global News Title', style: TextStyle(fontSize: 22, fontWeight: FontWeight.bold)),
            SizedBox(height: 12),
            Text('This content is generated and translated automatically using AI from public global sources.'),
            SizedBox(height: 20),
            Text('Source: Reuters | Language: ID / EN'),
          ],
        ),
      ),
    );
  }
}
lib/widgets/news_card.dart
import 'package:flutter/material.dart';

class NewsCard extends StatelessWidget {
  final String title;
  final String summary;
  final VoidCallback onTap;

  const NewsCard({super.key, required this.title, required this.summary, required this.onTap});

  @override
  Widget build(BuildContext context) {
    return Card(
      margin: const EdgeInsets.symmetric(vertical: 8, horizontal: 12),
      child: ListTile(
        title: Text(title),
        subtitle: Text(summary),
        onTap: onTap,
      ),
    );
  }
}
README.md
# Global Summary - Flutter App
Real-time global news app (ID + EN)
Build & deploy instructions:

1. Install Flutter (Termux / Codespaces)
2. Run `flutter pub get`
3. Add Firebase config (google-services.json, GoogleService-Info.plist)
4. Build APK: `flutter build apk --release`
5. Build AAB: `flutter build appbundle --release`
6. Upload `.aab` to Google Play Console
