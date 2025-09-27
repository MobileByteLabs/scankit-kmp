# ScanKit KMP 🔍

[![Kotlin](https://img.shields.io/badge/Kotlin-Multiplatform-blue)](https://kotlinlang.org/docs/multiplatform.html)
[![Compose](https://img.shields.io/badge/Jetpack-Compose-green)](https://developer.android.com/jetpack/compose)
[![License](https://img.shields.io/badge/License-Apache%202.0-orange)](LICENSE)
[![Maven Central](https://img.shields.io/maven-central/v/io.github.mobilebytelabs/scankit-kmp)](https://search.maven.org/artifact/io.github.mobilebytelabs/scankit-kmp)

A modern, cross-platform barcode scanner library built with Kotlin Multiplatform and Jetpack Compose. Scan QR codes and barcodes efficiently across Android, iOS, and Desktop platforms with a clean, customizable API following Clean Architecture principles.

## ✨ Features

- 📱 **Cross-Platform Support**: Android, iOS, and Desktop (JVM)
- 🎨 **Jetpack Compose UI**: Modern declarative UI components
- 📊 **Multiple Formats**: QR codes, Data Matrix, Code 128, EAN-13, UPC-A, and more
- 📷 **Real-time Scanning**: Live camera feed with instant detection
- 🔧 **Highly Customizable**: Configurable UI, themes, and scanning parameters
- 🏗️ **Clean Architecture**: SOLID principles with dependency injection support
- 🎯 **Performance Optimized**: Efficient scanning with minimal battery usage
- 📖 **Comprehensive Documentation**: Detailed guides and API documentation
- 🌙 **Material Design 3**: Full theming support including dark mode
- ♿ **Accessibility Ready**: Built-in accessibility features and testing

## 🚀 Platform Support

| Platform | Status | Min Version |
|----------|--------|-------------|
| Android  | ✅     | API 21+     |
| iOS      | ✅     | iOS 13+     |
| Desktop  | ✅     | JVM 11+     |

## 📦 Installation

### Gradle (Kotlin DSL)

Add the dependency to your `build.gradle.kts`:

```kotlin
dependencies {
    implementation("io.github.mobilebytelabs:scankit-kmp:1.0.0")
    
    // For Compose UI components
    implementation("io.github.mobilebytelabs:scankit-compose:1.0.0")
}
```

### Version Catalog

```toml
[versions]
scankit = "1.0.0"

[libraries]
scankit-kmp = { group = "io.github.mobilebytelabs", name = "scankit-kmp", version.ref = "scankit" }
scankit-compose = { group = "io.github.mobilebytelabs", name = "scankit-compose", version.ref = "scankit" }
```

## 🎯 Quick Start

### Basic Scanner Implementation

```kotlin
@Composable
fun BarcodeScanner(
    onBarcodeDetected: (Barcode) -> Unit,
    modifier: Modifier = Modifier,
    scannerConfig: ScannerConfig = ScannerConfig(),
    overlayContent: @Composable BoxScope.() -> Unit = { DefaultScannerOverlay() }
) {
    ScanKitCameraView(
        onBarcodeDetected = onBarcodeDetected,
        config = scannerConfig,
        modifier = modifier,
        overlayContent = overlayContent
    )
}

@Composable
fun ScannerScreen(
    onNavigateBack: () -> Unit,
    onBarcodeScanned: (String) -> Unit,
    modifier: Modifier = Modifier
) {
    val context = LocalContext.current
    
    Column(
        modifier = modifier.fillMaxSize()
    ) {
        BarcodeScanner(
            onBarcodeDetected = { barcode ->
                onBarcodeScanned(barcode.displayValue)
            },
            modifier = Modifier.weight(1f),
            scannerConfig = ScannerConfig(
                supportedFormats = setOf(
                    BarcodeFormat.QR_CODE,
                    BarcodeFormat.EAN_13,
                    BarcodeFormat.CODE_128
                ),
                enableTorch = true,
                enableAutoFocus = true
            )
        )
        
        ScannerControls(
            onNavigateBack = onNavigateBack,
            modifier = Modifier.fillMaxWidth()
        )
    }
}
```

### Custom Scanner Overlay

```kotlin
@Composable
fun CustomScannerOverlay(
    isScanning: Boolean = true,
    overlayColor: Color = MaterialTheme.colorScheme.surface.copy(alpha = 0.8f),
    scanFrameColor: Color = MaterialTheme.colorScheme.primary,
    scanFrameSize: Dp = 250.dp,
    cornerRadius: Dp = 16.dp,
    animationDuration: Int = 2000
) {
    Box(
        modifier = Modifier.fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
        // Semi-transparent overlay
        Canvas(modifier = Modifier.fillMaxSize()) {
            val scanFramePx = scanFrameSize.toPx()
            val centerX = size.width / 2f
            val centerY = size.height / 2f
            
            // Draw overlay with cutout
            drawRect(
                color = overlayColor,
                size = size
            )
            
            // Draw scan frame
            drawRoundRect(
                color = scanFrameColor,
                topLeft = Offset(
                    centerX - scanFramePx / 2f,
                    centerY - scanFramePx / 2f
                ),
                size = Size(scanFramePx, scanFramePx),
                cornerRadius = CornerRadius(cornerRadius.toPx()),
                style = Stroke(width = 4.dp.toPx())
            )
        }
        
        // Scanning instruction text
        Text(
            text = stringResource(R.string.scan_instruction),
            style = MaterialTheme.typography.bodyLarge,
            color = MaterialTheme.colorScheme.onSurface,
            modifier = Modifier
                .align(Alignment.BottomCenter)
                .padding(bottom = 100.dp)
        )
    }
}
```

### Configuration

```kotlin
data class ScannerConfig(
    val supportedFormats: Set<BarcodeFormat> = BarcodeFormat.ALL_FORMATS,
    val enableTorch: Boolean = false,
    val enableAutoFocus: Boolean = true,
    val enableBeep: Boolean = true,
    val enableVibration: Boolean = true,
    val scanDelay: Long = 1000L,
    val cameraFacing: CameraFacing = CameraFacing.BACK,
    val imageQuality: ImageQuality = ImageQuality.MEDIUM
)

enum class BarcodeFormat {
    QR_CODE,
    DATA_MATRIX,
    CODE_128,
    CODE_39,
    EAN_13,
    EAN_8,
    UPC_A,
    UPC_E,
    PDF_417,
    AZTEC,
    CODABAR,
    ITF,
    RSS_14,
    RSS_EXPANDED;
    
    companion object {
        val ALL_FORMATS = values().toSet()
        val COMMON_FORMATS = setOf(QR_CODE, EAN_13, CODE_128)
    }
}
```

## 🏗️ Architecture

ScanKit KMP follows Clean Architecture principles with clear separation of concerns:

```
┌─────────────────┐
│   Presentation  │  ← Compose UI Components
├─────────────────┤
│    Domain       │  ← Use Cases, Entities, Repositories (Interfaces)
├─────────────────┤
│      Data       │  ← Repository Implementations, Data Sources
├─────────────────┤
│   Framework     │  ← Platform-specific implementations
└─────────────────┘
```

### Dependency Injection

The library is designed to work with popular DI frameworks:

#### Koin Example

```kotlin
val scanKitModule = module {
    single<BarcodeScanner> { PlatformBarcodeScanner() }
    single<ScannerRepository> { ScannerRepositoryImpl(get()) }
    factory { ScanBarcodeUseCase(get()) }
}
```

#### Dagger/Hilt Example

```kotlin
@Module
@InstallIn(SingletonComponent::class)
abstract class ScanKitModule {
    
    @Binds
    abstract fun bindScannerRepository(
        scannerRepositoryImpl: ScannerRepositoryImpl
    ): ScannerRepository
}
```

## 🎨 Theming

ScanKit KMP fully supports Material Design 3 theming:

```kotlin
@Composable
fun ThemedScannerScreen() {
    MaterialTheme(
        colorScheme = dynamicColorScheme(),
        typography = AppTypography,
        shapes = AppShapes
    ) {
        BarcodeScanner(
            onBarcodeDetected = { /* handle result */ },
            scannerConfig = ScannerConfig(),
            overlayContent = {
                CustomScannerOverlay(
                    scanFrameColor = MaterialTheme.colorScheme.primary,
                    overlayColor = MaterialTheme.colorScheme.surface.copy(alpha = 0.8f)
                )
            }
        )
    }
}
```

## 📱 Platform-Specific Usage

### Android Permissions

Add required permissions to `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera" />
<uses-feature android:name="android.hardware.camera.autofocus" />
```

### iOS Permissions

Add camera usage description to `Info.plist`:

```xml
<key>NSCameraUsageDescription</key>
<string>This app needs camera access to scan barcodes</string>
```

### Permission Handling

```kotlin
@Composable
fun ScannerWithPermissions(
    onBarcodeScanned: (String) -> Unit
) {
    val cameraPermissionState = rememberPermissionState(
        android.Manifest.permission.CAMERA
    )
    
    LaunchedEffect(Unit) {
        cameraPermissionState.launchPermissionRequest()
    }
    
    when {
        cameraPermissionState.hasPermission -> {
            BarcodeScanner(
                onBarcodeDetected = { barcode ->
                    onBarcodeScanned(barcode.displayValue)
                }
            )
        }
        cameraPermissionState.shouldShowRationale -> {
            PermissionRationaleDialog(
                onRequestPermission = {
                    cameraPermissionState.launchPermissionRequest()
                }
            )
        }
        else -> {
            PermissionDeniedContent()
        }
    }
}
```

## 🧪 Testing

### Unit Tests

```kotlin
class ScanBarcodeUseCaseTest {
    
    @Test
    fun `should return success when barcode is valid`() = runTest {
        // Given
        val mockRepository = mockk<ScannerRepository>()
        val useCase = ScanBarcodeUseCase(mockRepository)
        val testBarcode = Barcode(
            format = BarcodeFormat.QR_CODE,
            displayValue = "test_value"
        )
        
        coEvery { mockRepository.scanBarcode(any()) } returns Result.success(testBarcode)
        
        // When
        val result = useCase.execute(ByteArray(0))
        
        // Then
        assertTrue(result.isSuccess)
        assertEquals("test_value", result.getOrNull()?.displayValue)
    }
}
```

### UI Tests

```kotlin
@RunWith(AndroidJUnit4::class)
class ScannerScreenTest {
    
    @get:Rule
    val composeTestRule = createComposeRule()
    
    @Test
    fun scannerScreen_displaysCorrectly() {
        composeTestRule.setContent {
            ScannerScreen(
                onNavigateBack = {},
                onBarcodeScanned = {}
            )
        }
        
        composeTestRule
            .onNodeWithContentDescription("Scanner overlay")
            .assertIsDisplayed()
    }
}
```

## 🛠️ Advanced Usage

### Custom Result Processing

```kotlin
class BarcodeProcessor {
    
    suspend fun processBarcode(
        barcode: Barcode,
        processingConfig: ProcessingConfig = ProcessingConfig()
    ): ProcessedResult {
        return when (barcode.format) {
            BarcodeFormat.QR_CODE -> processQRCode(barcode)
            BarcodeFormat.EAN_13 -> processProductCode(barcode)
            else -> ProcessedResult.Raw(barcode.displayValue)
        }
    }
    
    private suspend fun processQRCode(barcode: Barcode): ProcessedResult {
        return when {
            barcode.displayValue.startsWith("http") -> 
                ProcessedResult.Url(barcode.displayValue)
            barcode.displayValue.contains("@") -> 
                ProcessedResult.Email(barcode.displayValue)
            else -> ProcessedResult.Text(barcode.displayValue)
        }
    }
}
```

### Batch Scanning

```kotlin
@Composable
fun BatchScanner(
    onBatchComplete: (List<Barcode>) -> Unit,
    batchSize: Int = 10,
    modifier: Modifier = Modifier
) {
    var scannedCodes by remember { mutableStateOf(listOf<Barcode>()) }
    
    LaunchedEffect(scannedCodes.size) {
        if (scannedCodes.size >= batchSize) {
            onBatchComplete(scannedCodes)
            scannedCodes = emptyList()
        }
    }
    
    BarcodeScanner(
        onBarcodeDetected = { barcode ->
            if (!scannedCodes.any { it.displayValue == barcode.displayValue }) {
                scannedCodes = scannedCodes + barcode
            }
        },
        modifier = modifier
    )
}
```

## 📚 Sample Projects

Check out our sample projects in the `/samples` directory:

- **`basic-scanner`**: Simple QR code scanner
- **`product-scanner`**: E-commerce barcode scanner
- **`multi-format-scanner`**: Scanner supporting multiple formats
- **`custom-ui-scanner`**: Advanced UI customization examples

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

### Development Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/mobilebytelabs/scankit-kmp.git
   cd scankit-kmp
   ```

2. Set up the development environment:
   ```bash
   ./gradlew build
   ```

3. Run tests:
   ```bash
   ./gradlew allTests
   ```

4. Format code:
   ```bash
   ./gradlew spotlessApply
   ```

### Code Style

This project follows [Kotlin Coding Conventions](https://kotlinlang.org/docs/coding-conventions.html) and uses:
- [Spotless](https://github.com/diffplug/spotless) for code formatting
- [Detekt](https://detekt.dev/) for static analysis
- [ktlint](https://ktlint.github.io/) for Kotlin linting

## 🙏 Acknowledgments

- [ZXing](https://github.com/zxing/zxing) for barcode scanning algorithms
- [Jetpack Compose](https://developer.android.com/jetpack/compose) for modern UI toolkit
- [Kotlin Multiplatform](https://kotlinlang.org/docs/multiplatform.html) for cross-platform development

## 📞 Support

- 📖 [Documentation](https://mobilebytelabs.github.io/scankit-kmp)
- 🐛 [Issue Tracker](https://github.com/mobilebytelabs/scankit-kmp/issues)
- 💬 [Discussions](https://github.com/mobilebytelabs/scankit-kmp/discussions)
- 📧 [Email Support](mailto:mobilebytelabs@gmail.com)

---

<div align="center">
Made with ❤️ by the MobileByteLabs team
</div>
