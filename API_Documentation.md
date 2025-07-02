# Final Project - iOS Diary App API Documentation

## Overview

This iOS diary application allows users to create daily journal entries with mood tracking, photo attachments, and calendar navigation. The app integrates with Firebase for data storage and cloud functionality.

## Architecture

- **Language**: Swift
- **UI Framework**: UIKit
- **Backend**: Firebase (Firestore, Storage, Core)
- **Design Pattern**: MVC (Model-View-Controller)
- **Target Platform**: iOS

---

## Core Components

### 1. ViewController (Main Calendar View)

The primary view controller that displays a calendar interface and inspirational quotes.

#### Public Properties

```swift
@IBOutlet var viewBackground: UIView!
@IBOutlet weak var collectionView: UICollectionView!
@IBOutlet weak var placeholder: UILabel!
@IBOutlet weak var mottoTextField: UITextView!
@IBOutlet weak var mottoLabel: UILabel!
@IBOutlet weak var mottoUIView: UIView!
@IBOutlet weak var writingLabel: UILabel!
@IBOutlet weak var writingUIView: UIView!
@IBOutlet weak var monthLabel: UILabel!
@IBOutlet weak var nextbtn: UIButton!
@IBOutlet weak var previous: UIButton!

var selectedDate: Date
var totalSquares: [String]
var today: Date
var dateFormatter: DateFormatter
```

#### Public Methods

##### `viewDidLoad()`
Initializes the main view with UI styling and calendar setup.

**Usage:**
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    // Sets up corner radius for UI elements
    // Configures calendar view
    // Loads inspirational quote
    // Checks if diary entry exists for today
}
```

##### `setCellsView()`
Configures the calendar collection view cell dimensions.

**Usage:**
```swift
func setCellsView() {
    let width = (collectionView.frame.size.width - 2) / 8
    let height = (collectionView.frame.size.height - 2) / 8
    // Applies calculated dimensions to flow layout
}
```

##### `setMonthView()`
Updates the calendar display for the selected month.

**Usage:**
```swift
func setMonthView() {
    // Calculates days in month and starting position
    // Populates totalSquares array with day numbers
    // Updates month label and reloads collection view
}
```

##### `randomString()`
Displays a random inspirational quote.

**Usage:**
```swift
func randomString() {
    let quotes = [
        "Life is better when you smile :)",
        "The key to being happy is knowing you have the power to choose...",
        // ... more quotes
    ]
    mottoTextField.text = quotes.randomElement()
}
```

#### Collection View Data Source Methods

##### `collectionView(_:numberOfItemsInSection:) -> Int`
Returns the number of calendar cells (42 for 6 weeks).

##### `collectionView(_:cellForItemAt:) -> UICollectionViewCell`
Configures calendar cells with day numbers.

**Parameters:**
- `collectionView`: The collection view requesting the cell
- `indexPath`: Location of the cell in the collection view

**Returns:** Configured `CalendarCollectionViewCell`

##### `collectionView(_:didSelectItemAt:)`
Handles calendar date selection and navigates to diary entry if exists.

**Usage:**
```swift
func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath) {
    let diaryDate = diaryDateFormat(year: year, month: month, date: selectedDate)
    // Checks Firebase for existing entry
    // Navigates to CheckViewController if entry exists
}
```

#### Actions

##### `tapAction(_:)`
Navigates to the writing view controller.

##### `previousMonth(_:)` / `nextMonth(_:)`
Navigate between calendar months.

---

### 2. WritingViewController (Diary Entry Creation)

Handles creation of new diary entries with text, mood selection, and photo upload.

#### Public Properties

```swift
@IBOutlet weak var contentIng: UIImageView!
@IBOutlet weak var pickedImg: UIImageView!
@IBOutlet weak var uploadPicBtn: UIButton!
@IBOutlet weak var finishEditBtn: UIButton!
@IBOutlet weak var textFieldContent: UITextView!
@IBOutlet weak var EmojiCollectionView: UICollectionView!
@IBOutlet weak var selectImgView: UIView!

let today: Date
let dateFormatter: DateFormatter
private let storage: Storage.storage().reference()
```

#### Static Variables

```swift
struct myVariables {
    static var mood: String = ""
    static var urlString: String = ""
}
```

#### Public Methods

##### `viewDidLoad()`
Initializes the writing interface with UI styling and delegates.

**Usage:**
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    // Applies corner radius and colors to UI elements
    // Sets up collection view delegates
}
```

##### Collection View Methods

##### `collectionView(_:numberOfItemsInSection:) -> Int`
Returns the number of emoji options available.

##### `collectionView(_:cellForItemAt:) -> UICollectionViewCell`
Configures emoji selection cells.

**Usage:**
```swift
func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
    let cell = EmojiCollectionView.dequeueReusableCell(withReuseIdentifier: "Cell", for: indexPath) as! EmojisCollectionViewCell
    cell.setup(with: emojis[indexPath.row])
    return cell
}
```

##### `collectionView(_:didSelectItemAt:)`
Handles mood emoji selection and visual feedback.

**Parameters:**
- `indexPath`: Selected emoji index

**Behavior:**
- Highlights selected cell
- Stores mood in `myVariables.mood`
- Saves to UserDefaults

#### Image Handling Methods

##### `showImagePickerController()`
Presents image picker for photo selection.

**Usage:**
```swift
func showImagePickerController() {
    let imagePickerController = UIImagePickerController()
    imagePickerController.delegate = self
    imagePickerController.allowsEditing = true
    imagePickerController.sourceType = .photoLibrary
    present(imagePickerController, animated: true, completion: nil)
}
```

##### `imagePickerController(_:didFinishPickingMediaWithInfo:)`
Handles selected image and uploads to Firebase Storage.

**Parameters:**
- `picker`: The image picker controller
- `info`: Dictionary containing image information

**Behavior:**
- Converts image to PNG data
- Uploads to Firebase Storage with date-based filename
- Retrieves and stores download URL

#### Utility Methods

##### `showAlert()`
Displays alert for mood selection requirement.

##### `finishEdited(_:)`
Saves diary entry to Firebase Firestore.

**Data Saved:**
- Date (YYYY-MM-DD format)
- Text content
- Selected mood
- Image URL

---

### 3. CheckViewController (Diary Entry Display)

Displays existing diary entries with mood, text, and photos.

#### Public Properties

```swift
@IBOutlet weak var moodImageView: UIImageView!
@IBOutlet weak var checkTextView: UITextView!
@IBOutlet weak var picView: UIImageView!

var year: String = ""
var month: String = ""
var date: String = ""
var mood: String = ""
var content: String = ""
var imageurl: String = ""
var diaryDate: String = ""
```

#### Public Methods

##### `viewDidLoad()`
Loads and displays diary entry data from Firebase.

**Usage:**
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    // Applies UI styling
    // Formats diary date
    // Queries Firebase for entry data
    // Updates UI with retrieved data
}
```

**Firebase Query:**
```swift
db.collection("user").whereField("date", isEqualTo: diaryDate).getDocuments { (snapshot, error) in
    // Processes document data
    // Updates mood image, text content, and photo
}
```

---

## Data Models and Utilities

### 4. Emojis Structure

Defines the mood emoji data structure.

#### Public Structure

```swift
struct Emojis {
    let name: String
    let image: UIImage
}
```

#### Global Array

```swift
let emojis: [Emojis] = [
    Emojis(name: "Eye Roll Emoji", image: #imageLiteral(resourceName: "Eye Roll Emoji")),
    Emojis(name: "Sad Face Emoji", image: #imageLiteral(resourceName: "Sad Face Emoji")),
    // ... 16 total emoji options
]
```

**Available Moods:**
- Eye Roll Emoji
- Sad Face Emoji
- Sick Emoji
- Sleeping with Snoring Emoji
- Slightly Smiling Face Emoji
- Smiling Face with Tightly Closed eyes
- Upside-Down Face Emoji
- Very Angry Emoji
- Tears of Joy Emoji
- Persevering Face Emoji
- Loudly Crying Face Emoji
- Kiss Emoji with Closed Eyes
- Heart Eyes Emoji
- Grinmacing Face Emoji
- Fearful Face Emoji

### 5. DiaryDate Utility

Provides date formatting functionality for diary entries.

#### Global Variables

```swift
var monthInt: String = ""
var diaryDate: String = ""
```

#### Public Functions

##### `diaryDateFormat(year:month:date:) -> String`
Converts readable date format to database-compatible format.

**Parameters:**
- `year`: String representation of year
- `month`: Full month name (e.g., "January")
- `date`: Day of month as string

**Returns:** Formatted date string (YYYY-MM-DD)

**Usage:**
```swift
let formattedDate = diaryDateFormat(year: "2021", month: "June", date: "15")
// Returns: "2021-06-15"
```

**Month Mapping:**
- January → 01
- February → 02 (Note: spelled as "Febuary" in code)
- March → 03
- April → 04
- May → 05
- June → 06
- July → 07
- August → 08
- September → 09
- October → 10
- November → 11
- December → 12

---

## UI Components

### 6. EmojisCollectionViewCell

Custom collection view cell for emoji mood selection.

#### Public Properties

```swift
@IBOutlet weak var emojiImage: UIImageView!
```

#### Public Methods

##### `setup(with:)`
Configures cell with emoji data.

**Parameters:**
- `emojis`: Emojis struct containing name and image

**Usage:**
```swift
func setup(with emojis: Emojis) {
    emojiImage.image = emojis.image
}
```

### 7. PaddingUITextView

Custom UITextView with built-in padding.

#### Public Properties

```swift
textContainerInset: UIEdgeInsets
```

#### Initialization

```swift
required init?(coder aDecoder: NSCoder) {
    super.init(coder: aDecoder)
    textContainerInset = UIEdgeInsets(top: 15, left: 15, bottom: 15, right: 15)
}
```

**Purpose:** Provides consistent 15-point padding on all sides for text views.

---

## Extensions

### 8. UIImageView Extension

Adds URL-based image loading capability.

#### Public Methods

##### `load(urlString:)`
Asynchronously loads image from URL string.

**Parameters:**
- `urlString`: URL string of the image to load

**Usage:**
```swift
imageView.load(urlString: "https://firebase.storage.url/image.png")
```

**Implementation Details:**
- Performs network request on background queue
- Updates UI on main queue
- Handles URL validation and error cases
- Uses weak self reference to prevent retain cycles

```swift
func load(urlString: String) {
    guard let url = URL(string: urlString) else { return }
    DispatchQueue.global().async { [weak self] in
        if let data = try? Data(contentsOf: url) {
            if let image = UIImage(data: data) {
                DispatchQueue.main.async {
                    self?.image = image
                }
            }
        }
    }
}
```

---

## App Configuration

### 9. AppDelegate

Main application delegate with Firebase configuration.

#### Key Features

##### Firebase Initialization
```swift
override init() {
    super.init()
    FirebaseApp.configure()
}
```

##### Application Lifecycle
- Handles app launch completion
- Manages scene session lifecycle
- Configures default scene configuration

### 10. SceneDelegate

Manages scene lifecycle for iOS 13+ scene-based architecture.

#### Key Methods

- `scene(_:willConnectTo:options:)`: Scene connection setup
- `sceneDidBecomeActive(_:)`: Scene activation handling
- `sceneDidEnterBackground(_:)`: Background transition handling

---

## Firebase Integration

### Database Structure

#### Collection: "user"
Document ID format: `YYYY-MM-DD`

**Document Fields:**
- `date`: String (YYYY-MM-DD format)
- `content`: String (diary entry text)
- `mood`: String (emoji name)
- `imageurl`: String (Firebase Storage URL)

### Storage Structure

#### Path: "images/"
File naming: `file{YYYY-MM-DD}.png`

**Example:** `images/file2021-06-15.png`

---

## Dependencies

### CocoaPods Dependencies

```ruby
pod 'Firebase'
pod 'Firebase/Core'
pod 'Firebase/Database'
pod 'Firebase/Firestore'
pod 'Firebase/Storage'
```

### Required Capabilities

- Firebase Firestore for data storage
- Firebase Storage for image hosting
- Photo library access for image selection
- Network connectivity for Firebase operations

---

## Usage Examples

### Creating a New Diary Entry

```swift
// 1. User navigates to WritingViewController
// 2. User enters text content
textFieldContent.text = "Today was a great day!"

// 3. User selects mood emoji
// This happens through collection view selection

// 4. User optionally adds photo
// This triggers image picker and upload

// 5. User saves entry
@IBAction func finishEdited(_ sender: Any) {
    // Validates mood selection
    // Uploads to Firebase
    // Returns to main view
}
```

### Viewing Existing Entry

```swift
// 1. User taps date on calendar
// 2. System checks Firebase for entry
let db = Firestore.firestore()
let docRef = db.collection("user").document(diaryDate)
docRef.getDocument { (document, error) in
    if document.exists {
        // Navigate to CheckViewController
        self.performSegue(withIdentifier: "viewRecord", sender: indexPath)
    }
}
```

### Date Formatting

```swift
// Convert user-readable date to database format
let dbDate = diaryDateFormat(year: "2021", month: "June", date: "15")
// Result: "2021-06-15"
```

---

## Error Handling

### Common Error Scenarios

1. **Missing Mood Selection**: Alert displayed requesting mood selection
2. **Network Connectivity**: Firebase operations may fail silently
3. **Image Upload Failures**: Logged to console but not shown to user
4. **Invalid Date Formats**: Handled by date formatting function

### Recommended Improvements

1. Add comprehensive error handling for Firebase operations
2. Implement user feedback for network-related failures
3. Add input validation for diary content
4. Implement retry mechanisms for failed uploads

---

## Performance Considerations

### Image Loading
- Images loaded asynchronously to prevent UI blocking
- Weak references used to prevent memory leaks

### Firebase Queries
- Single document queries for optimal performance
- Date-based document IDs for efficient retrieval

### Memory Management
- Proper delegate patterns implemented
- Image data converted to PNG for consistent handling

---

## Security Notes

### Firebase Security
- App currently uses default Firebase security rules
- Consider implementing user authentication for production
- Validate uploaded image types and sizes

### Data Privacy
- User data stored in Firebase cloud
- No local data encryption implemented
- Consider implementing data anonymization