<!-- 
This README describes the package. If you publish this package to pub.dev,
this README's contents appear on the landing page for your package.

For information about how to write a good package README, see the guide for
[writing package pages](https://dart.dev/guides/libraries/writing-package-pages). 

For general information about developing packages, see the Dart guide for
[creating packages](https://dart.dev/guides/libraries/create-library-packages)
and the Flutter guide for
[developing packages and plugins](https://flutter.dev/developing-packages). 
-->
# Tabby Flutter SDK

Use the Tabby checkout in your Flutter app.

## Requirements

- Dart sdk: `">=2.14.0 <3.0.0"`
- Flutter: `">=2.5.0"`
- Android: `minSdkVersion 17` and add support for `androidx` (see [AndroidX Migration](https://flutter.dev/docs/development/androidx-migration) to migrate an existing app)
- iOS: `--ios-language swift`, Xcode version `>= 12`

## Getting started

Add `flutter_inappwebview` as a [dependency in your pubspec.yaml file](https://flutter.io/using-packages/).

In your `android/app/src/main/AndroidManifest.xml`

```xml
    .....
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />


    <application ....>
        <!-- Add a provider to be able to upload a user id photo -->
        <provider
            android:name="androidx.core.content.FileProvider"
            android:authorities="${applicationId}.flutter_inappwebview.fileprovider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/provider_paths" />
        </provider>
     .....
```

## Usage

1) You should initialise Tabby SDK. We recommend to do it in `main.dart` file:

```dart
  TabbySDK().setup(
    withApiKey: '', // Put here your Api key
    environment: Environment.stage, // Or use Environment.production
  );
```

2) Create checkout session:

```dart
  final mockPayload = Payment(
    amount: '340',
    currency: Currency.aed,
    buyer: Buyer(
      email: 'id.card.success@tabby.ai',
      phone: '500000001',
      name: 'Yazan Khalid',
      dob: '2019-08-24',
    ),
    buyerHistory: BuyerHistory(
      loyaltyLevel: 0,
      registeredSince: '2019-08-24T14:15:22Z',
      wishlistCount: 0,
    ),
    shippingAddress: const ShippingAddress(
      city: 'string',
      address: 'string',
      zip: 'string',
    ),
    order: Order(referenceId: 'id123', items: [
      OrderItem(
        title: 'Jersey',
        description: 'Jersey',
        quantity: 1,
        unitPrice: '10.00',
        referenceId: 'uuid',
        productUrl: 'http://example.com',
        category: 'clothes',
      )
    ]),
    orderHistory: [
      OrderHistoryItem(
        purchasedAt: '2019-08-24T14:15:22Z',
        amount: '10.00',
        paymentMethod: OrderHistoryItemPaymentMethod.card,
        status: OrderHistoryItemStatus.newOne,
      )
    ],
  );

  final session = await TabbySDK().createSession(TabbyCheckoutPayload(
    merchantCode: 'ae',
    lang: Lang.en,
    payment: mockPayload,
  ));
```

3) Open on app browser to show checkout: 

```dart
  void openInAppBrowser() {
    TabbyWebView.showWebView(
      context: context,
      webUrl: session.availableProducts.installments.webUrl,
      onResult: (WebViewResult resultCode) {
        print(resultCode.name);
        // TODO: Process resultCode
      },
    );
  }
```

Also you can use TabbyWebView as inline widget on your page:

```dart
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Tabby Checkout'),
      ),
      body: TabbyWebView(
        webUrl: session.availableProducts.installments.webUrl,
        onResult: (WebViewResult resultCode) {
          print(resultCode.name);
          // TODO: Process resultCode
        },
      ),
    );
  }
```

### Snippet
<p>
  <img src="./docs/snippet_en.png" width="375" title="english button">
  <img src="./docs/snippet_ar.png" width="375" title="arabic button">
</p>


We recommend use `TabbyCheckoutButton` as an entry point to the Tabby checkout:

```dart
  TabbyCheckoutButton(
    onPressed: openInAppBrowser,
    price: mockPayload.amount,
    currency: mockPayload.currency,
    lang: lang,
  )
```

## Example

You can also check the [example project](https://github.com/tabby-ai/tabby-flutter-sdk/tree/master/example).