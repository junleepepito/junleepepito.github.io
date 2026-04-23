# Comprehensive Development Plan: Expiration Date OCR Integration

## 1. Project Objective
Build and integrate a native OCR (Optical Character Recognition) feature into an existing Expo application. The primary goal is to scan product labels, identify expiration dates (e.g., "CBD 02NOV26"), and validate them against current timeframes to ensure inventory accuracy for a retail business.

## 2. Technical Stack
- **Framework:** Expo (SDK 50+)
- **Build System:** EAS (Expo Application Services)
- **Camera:** `expo-camera` (for capture and torch control)
- **OCR Engine:** `@react-native-ml-kit/text-recognition` (Native Android/iOS performance)
- **Environment:** Development Clients (Required for native modules)

## 3. Implementation Steps

### Phase 1: Environment & Permissions
1. Install dependencies: `npx expo install expo-camera @react-native-ml-kit/text-recognition`.
2. Configure `app.json`:
   - Add `expo-camera` plugin with `cameraPermission`.
   - Set Android `package` name for EAS compatibility.
   - Configure native text recognition plugins.

### Phase 2: Camera & Scanner UI
1. Create a `ScannerView` component.
2. Implement a "Viewfinder" overlay to help users align labels.
3. Add a Torch toggle (Flash) for low-light scanning.
4. Capture high-resolution still images to ensure clear text for the OCR engine.

### Phase 3: Parsing Logic (The Engine)
1. **Extraction:** Use `TextRecognition.recognize(uri)` to extract raw strings.
2. **Regex Matching:** Implement a utility to find the pattern `(\d{2})([A-Z]{3})(\d{2})`.
3. **Format Mapping:** Create a dictionary to map short month names (NOV, DEC, etc.) to month indices.
4. **Year Normalization:** Convert 2-digit years (e.g., "26") to 4-digit years ("2026").

### Phase 4: The "Confidence Check" (Pro-Tip)
To avoid confusion with batch codes or manufacturing years (like "26" at the start of a lot number), implement the following validation:
- **Future Check:** If the detected year is more than 5 years in the future, flag it as "Likely Batch Code" and request manual verification.
- **Past Check:** If the detected year is more than 2 years in the past, flag it as "Expired/Check Label".
- **Context Search:** Prioritize strings found near keywords like "CBD" (Consumer Best Date), "EXP", "BB", or "Best Before".

### Phase 5: User Validation UI
- **Confirmation Modal:** Show the scanned raw text vs. the parsed date.
- **Color Coding:** - **Green:** Date is within a valid range.
  - **Yellow/Red:** Date failed the Confidence Check.
- **Manual Override:** Allow the user to edit the date if the OCR misreads a character (e.g., reading a '6' as a '5').

## 4. Testing & Build Workflow
1. **Development Build:** Use `eas build --profile development --platform android` to create a custom development client.
2. **Local Testing:** Install the resulting APK on a physical device (OCR will not work in standard Expo Go).
3. **Run Server:** Start the dev server using `npx expo start --dev-client`.

## 5. Final Prompt for AI Agent
"Act as a Senior React Native Developer. Using the plan above, generate the code for a 'DateScanner' module. 
1. Create a camera component that takes a photo.
2. Pass the photo to ML Kit Text Recognition.
3. Use Regex to find dates in 'DDMMMYY' format.
4. Implement the 'Confidence Check' logic: if the date is >5 years ahead or >2 years behind 2026, trigger a UI warning.
5. Return the finalized Date object for database storage."
