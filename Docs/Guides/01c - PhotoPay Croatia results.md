## Croatian payment slip scanning results

### Initializing the scanning of Croatian slips

To initialize the scanning of Croatian slips, use the following intialization code:

```objective-c
- (PPCoordinator*)createCoordinator {

    // Check if photopay is supported
    NSError *error;
    if ([PPCoordinator isScanningUnsupported:&error]) {
        NSString *messageString = [error localizedDescription];
        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Warning" message:messageString delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil, nil];
        [alert show];

        return nil;
    }

    // 1. ******* Instantiate Scanning settings ********/
    PPSettings* settings = [[PPSettings alloc] init];


    // 2. ************* Setup UI Settings **************/

    // Instantiate PhotoPay UI settings. This allows more customization in the initialization process.
    PPPhotoPayUiSettings* photopayUiSettings = [[PPPhotoPayUiSettings alloc] init];
    settings.uiSettings = photopayUiSettings;

    // Use croatian language
    photopayUiSettings.language = @"hr";

    // Help is available so present it just in the first app run
    photopayUiSettings.helpDisplayMode = PPHelpDisplayModeFirstRun;

    // Use Toast messages
    photopayUiSettings.presentToast = YES;

    // Autorotate overlay
    photopayUiSettings.autorotateOverlay = YES;


    // 3. ************* Setup Scan Settings **************/

    // Add recognizer for Croatian payslips
    PPCroSlipRecognizerSettings *croSlipRecognizer = [[PPCroSlipRecognizerSettings alloc] init];
    [settings.scanSettings addRecognizerSettings:croSlipRecognizer];

    // Read payment description always
    croSlipRecognizer.readPaymentDescription = YES;

    // Sanitize OCR output to be by the HUB standard
    croSlipRecognizer.useSanitization = YES;


    // Add PDF417 recognizer for Croatian payslips
    PPCroPdf417RecognizerSettings *croPdf417Recognizer = [[PPCroPdf417RecognizerSettings alloc] init];
    [settings.scanSettings addRecognizerSettings:croPdf417Recognizer];

    // Sanitize PDF417 output to be by the HUB standard
    croPdf417Recognizer.useSanitization = YES;
    

    // Add QR code recognizer for Croatian payslips
    PPCroQrRecognizerSettings *croQrRecognizer = [[PPCroQrRecognizerSettings alloc] init];
    [settings.scanSettings addRecognizerSettings:croQrRecognizer];

    // Sanitize QR output to be by the HUB standard
    croQrRecognizer.useSanitization = YES;


    // 4. ************* Setup License Settings **************/

    // Set your license key here. This specific key is for demo purposes only!
    settings.licenseSettings.licenseKey = @"5KOE-BTSH-67OL-7F3L-KUAE-SK3G-TEN3-7FNP";

    // Allocate the recognition coordinator object
    PPCoordinator *coordinator = [[PPCoordinator alloc] initWithSettings:settings];
    
    return coordinator;
}

- (IBAction)didTapScan:(id)sender {
    PPCoordinator* coordinator = [self createCoordinator];
    if (coordinator == nil) {
        return;
    }

    // Create camera view controller
    UIViewController *cameraViewController = [coordinator cameraViewControllerWithDelegate:self];

    // present it
    cameraViewController.modalTransitionStyle = UIModalTransitionStyleCrossDissolve;
    [self presentViewController:cameraViewController animated:YES completion:nil];
}
```

### Retrieving results.

Scanning results for Austrian payslips are obtained as instances of two possible classes `PPAusSlipRecognizerResult` (if payment slip was scanned), or `PPAusQrRecognizerResult` (if QR code was scanned). See the header files or sample below for all fields contained in these objects.

```objective-c
- (void)scanningViewController:(UIViewController<PPScanningViewController> *)scanningViewController
              didOutputResults:(NSArray *)results {

    // find the recognition result and process it

    for (PPRecognizerResult* result in results) {

        if ([result isKindOfClass:[PPCroSlipRecognizerResult class]]) {
            [self processCroSlipRecognizerResult:(PPCroSlipRecognizerResult*)result];
        }
        
        // this handles both QR code and PDF417 results as they both inherit from PPCroBarcodeRecognizerResult
        
        if ([result isKindOfClass:[PPCroBarcodeRecognizerResult class]]) {
            [self processCroBarcodeRecognizerResult:(PPCroBarcodeRecognizerResult*)result];
        }
    };
}

- (void)processCroSlipRecognizerResult:(PPCroSlipRecognizerResult*)croSlipResult {

    // Here we log all fields in PPCroSlipRecognizerResult object

    NSLog(@"Croatian payment slip results\n");

    NSLog(@"Amount is %@", [croSlipResult amount]);
    NSLog(@"Currency is %@", [croSlipResult currency]);

    NSLog(@"Recipient name is %@", [croSlipResult recipientName]);
    NSLog(@"IBAN is %@", [croSlipResult iban]);
    NSLog(@"Bank code is %@", [croSlipResult bankCode]);
    NSLog(@"Account number is %@", [croSlipResult accountNumber]);
    NSLog(@"Reference number is %@", [croSlipResult referenceNumber]);
    NSLog(@"Reference model is %@", [croSlipResult referenceModel]);
    NSLog(@"Payment description is %@", [croSlipResult paymentDescription]);
    NSLog(@"Payment description code is %@", [croSlipResult paymentDescriptionCode]);

    NSLog(@"Purpose code is %@", [croSlipResult purposeCode]);
    NSLog(@"Payer name is %@", [croSlipResult payerName]);
}

- (void)processCroBarcodeRecognizerResult:(PPCroBarcodeRecognizerResult*)croBarcodeResult {

    // Here we log all fields in PPCroBarcodeRecognizerResult object

    NSLog(@"Croatian Barcode (PDF417 and QR code) results\n");

    NSLog(@"Amount is %@", [croBarcodeResult amount]);
    NSLog(@"Currency is %@", [croBarcodeResult currency]);

    NSLog(@"Recipient name is %@", [croBarcodeResult recipientName]);
    NSLog(@"IBAN is %@", [croBarcodeResult iban]);
    NSLog(@"Bank code is %@", [croBarcodeResult bankCode]);
    NSLog(@"Account number is %@", [croBarcodeResult accountNumber]);
    NSLog(@"Reference number is %@", [croBarcodeResult referenceNumber]);
    NSLog(@"Reference model is %@", [croBarcodeResult referenceModel]);
    NSLog(@"Payment description is %@", [croBarcodeResult paymentDescription]);
    NSLog(@"Payment description code is %@", [croBarcodeResult paymentDescriptionCode]);

    NSLog(@"Due date is %@", [croBarcodeResult dueDate]);
    NSLog(@"Recipient address is %@", [croBarcodeResult recipientAddress]);
    NSLog(@"Detailed address is %@", [croBarcodeResult recipientDetailedAddress]);

    NSLog(@"Optional data is %@", [croBarcodeResult optionalData]);
}

```