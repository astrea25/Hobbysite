# Hobbysite

Hobbysite is a comprehensive platform featuring the following sections:

- [Commission](#commission)
- [Forum](#forum)
- [Merchandise Store](#merchandise-store)
- [Wiki](#wiki)
- [User Authentication](#user-authentication)

Each section is built using Django for both the front-end and back-end, with specific functionalities as detailed below.

## Commission

**URLs:**

- **List View:** `/commissions/list`
- **Detail View:** `/commissions/detail/1`
- **Create View:** `/commissions/add`
- **Update View:** `/commissions/1/edit`

**Models:**

- **Commission**
  - `Title`: Maximum length of 255 characters
  - `Description`: Text field
  - `Status`: Character field with the following options:
    - `Open` (default)
    - `Full`
    - `Completed`
    - `Discontinued`
  - `Created On`: Datetime field, set upon creation
  - `Updated On`: Datetime field, updates on modification
  - **Sorting:** Commissions are sorted by creation date in ascending order

- **Job** 
  - `Commission`: Foreign key to Commission; deletion is cascaded
  - `Role`: Maximum length of 255 characters
  - `Manpower Required`: Whole number
  - `Status`: Character field with the following options:
    - `Open` (default)
    - `Full`
  - **Sorting:** Jobs are sorted by status (`Open` > `Full`), manpower required in descending order, then role in ascending order

- **JobApplication**
  - `Job`: Foreign key to Job; deletion is cascaded
  - `Applicant`: Foreign key to Profile; deletion is cascaded
  - `Status`: Character field with the following options:
    - `Pending` (default)
    - `Accepted`
    - `Rejected`
  - `Applied On`: Datetime field, set upon creation
  - **Sorting:** Applications are sorted by status (`Pending` > `Accepted` > `Rejected`), then by application date in descending order

**Views:**

- **Commission List View:**
  - Displays all commissions, sorted by:
    - Status (`Open` > `Full` > `Completed` > `Discontinued`)
    - Creation date, most recent first
  - For logged-in users:
    - Displays commissions created by the user
    - Displays commissions the user has applied to
  - Provides a link to create a new commission

- **Commission Detail View:**
  - Shows commission details and associated jobs
  - Displays total and available manpower
  - For logged-in users:
    - Provides an "Apply to Job" button (disabled if positions are filled)
    - If the user is the commission owner, provides an edit link

- **Create View:**
  - All fields are available; `Created On` and `Updated On` are auto-set
  - `Author` is the logged-in user; these fields are non-editable
  - `Status` is a dropdown
  - Allows creation of Job entries
  - Accessible only to logged-in users

- **Update View:**
  - All fields are editable except `Created On` and `Author`
  - If all jobs are `Full`, the commission's status is set to `Full`
  - Accessible only to logged-in users

## Forum

**URLs:**

- **List View:** `/forum/threads`
- **Detail View:** `/forum/thread/1`
- **Create View:** `/forum/thread/add`
- **Edit View:** `/forum/thread/1/edit`

**Models:**

- **ThreadCategory**
  - `Name`: Maximum length of 255 characters
  - `Description`: Text field
  - **Sorting:** Categories are sorted by name in ascending order

- **Thread** (formerly Post model)
  - `Title`: Maximum length of 255 characters
  - `Author`: Foreign key to Profile; set to NULL when deleted
  - `Category`: Foreign key to ThreadCategory; set to NULL when deleted
  - `Entry`: Text field
  - `Image`: Optional image field
  - `Created On`: Datetime field, set upon creation
  - `Updated On`: Datetime field, updates on modification
  - **Sorting:** Threads are sorted by creation date in descending order

- **Comment**
  - `Author`: Foreign key to Profile; set to NULL when deleted
  - `Thread`: Foreign key to Thread; deletion is cascaded
  - `Entry`: Text field
  - `Created On`: Datetime field, set upon creation
  - `Updated On`: Datetime field, updates on modification
  - **Sorting:** Comments are sorted by creation date in ascending order

**Views:**

- **Thread List View:**
  - Lists all threads, grouped by category; titles link to detail views
  - For logged-in users:
    - Displays user's threads in a separate group
    - Provides a link to create a new thread

- **Thread Detail View:**
  - Shows thread details and related posts
  - Provides a link back to the thread list
  - Displays existing comments and a comment form for logged-in users
  - If the user is the thread owner, provides an edit link

- **Thread Create View:**
  - All fields are available; `Created On` and `Updated On` are auto-set
  - `Author` is the logged-in user; these fields are non-editable
  - `Category` is a dropdown
  - Accessible only to logged-in users

- **Thread Update View:**
  - All fields are editable except `Created On` and `Author`
  - Accessible only to logged-in users

## Merchandise Store

**URLs:**

- **Product List View:** `/merchstore/items`
- **Product Detail View:** `/merchstore/item/1`
- **Product Create View:** `/merchstore/item/add`
- **Product Update View:** `/merchstore/item/1/edit`
- **Cart View:** `/merchstore/cart`
- **Transactions List View:** `/merchstore/transactions`

**Models:**

- **ProductType**
  - `Name`: Max length of 255 characters
  - `Description`: Text field
  - Sorted by name in ascending order

- **Product**
  - `Name`: Max length of 255 characters
  - `Product Type`: Foreign key to `ProductType`, set to NULL when deleted
  - `Owner`: Foreign key to `Profile`, model deletion is cascaded
  - `Description`: Text field
  - `Price`: Two decimal places
  - `Stock`: Whole number
  - `Status`: Character field with options:
    - Available (default)
    - On Sale
    - Out of Stock (when stock is 0)

- **Transaction**
  - `Buyer`: Foreign key to `Profile`, set to NULL when deleted
  - `Product`: Foreign key to `Product`, set to NULL when deleted
  - `Amount`: Whole number (amount of product to buy)
  - `Status`: Character field with options:
    - On Cart
    - To Pay
    - To Ship
    - To Receive
    - Delivered
  - `Created On`: Datetime field, set when created

**Views:**

- **Product List View**
  - Lists all products in the system
  - Products posted by the logged-in user are grouped separately and removed from the All Products list
  - Link available to create a new product

- **Product Detail View**
  - Users cannot purchase their own products
  - Displays a `Transaction` form for purchases
  - If stock is 0, the buy button is disabled
  - If the user owns the product, an edit link is provided

- **Product Create View**
  - Allows all fields to be filled except the `Owner` (set to logged-in user)
  - `Product Type` and `Status` are dropdown fields
  - Only accessible to logged-in users

- **Product Update View**
  - Allows updates to all fields except `Owner`
  - If stock is 0, status is automatically set to "Out of Stock"; otherwise, it's set to "Available"
  - Only accessible to logged-in users

- **Cart View**
  - Displays all `Transaction` entries where the logged-in user is the buyer
  - Transactions are categorized by the product's owner
  - Only accessible to logged-in users

- **Transaction List View**
  - Displays all `Transaction` entries where the logged-in user is the seller
  - Transactions are categorized by the product's buyer
  - Only accessible to logged-in users

---

## Wiki

**URLs:**

- **List View:** `/wiki/articles`
- **Detail View:** `/wiki/article/1`
- **Create View:** `/wiki/article/add`
- **Update View:** `/wiki/article/1/edit`

**Models:**

- **ArticleCategory**
  - `Name`: Max length of 255 characters
  - `Description`: Text field
  - Sorted by name in ascending order

- **Article**
  - `Title`: Max length of 255 characters
  - `Author`: Foreign key to `Profile`, set to NULL when deleted
  - `Category`: Foreign key to `ArticleCategory`, set to NULL when deleted
  - `Entry`: Text field
  - `Header Image`: Image field for article header
  - `Created On`: Datetime field, set when created
  - `Updated On`: Datetime field, updates on modification
  - Sorted by creation date in descending order

- **Comment**
  - `Author`: Foreign key to `Profile`, set to NULL when deleted
  - `Article`: Foreign key to `Article`, model deletion is cascaded
  - `Entry`: Text field
  - `Created On`: Datetime field, set when created
  - `Updated On`: Datetime field, updates on modification
  - Sorted by creation date in ascending order

**Views:**

- **Article List View**
  - Lists all articles in the system, grouped by category
  - Titles link to `Article Detail View`
  - Articles created by the logged-in user are displayed separately and removed from the main list
  - A link is available to create an article

- **Article Detail View**
  - Displays at least two links to related articles in the same category
  - Includes an article header image
  - Displays existing comments sorted from newest to oldest
  - Shows a comment form for logged-in users
  - If the logged-in user is the author, an edit link is provided

- **Article Create View**
  - Allows all fields to be filled except `Created On`, `Updated On`, and `Author`
  - `Category` field is a dropdown
  - Only accessible to logged-in users

- **Article Update View**
  - Allows updates to all fields except `Created On` and `Author`
  - Only accessible to logged-in users

  **Bonus:** A gallery feature for the wiki page can be added.

---

## Profile

**URLs:**
- **Update View:** `/profile`

**Models:**

- **Profile**
  - Extension of the `User` model
  - `Display Name`: Max length of 63 characters
  - `Email Address`: Email field
  - `Avatar`: Image field for profile picture
  - `Bio`: Text field for user biography
  - `Joined On`: Datetime field, automatically set when the profile is created
  - Sorted by `Display Name` in ascending order

**Views:**

- **Profile Update View**
  - Allows users to edit their profile details including:
    - Display Name
    - Email Address
    - Avatar (Profile Picture)
    - Bio
  - The `Joined On` field is displayed but cannot be edited
  - Only accessible to logged-in users
  - If the user does not have a profile, one is created upon first access

**Features:**
- **Profile Creation:** A profile is automatically generated when a user registers.
- **Profile Editing:** Users can update their display name, avatar, and bio.
- **Avatar Upload:** Users can upload and update their profile pictures.
- **User Authentication:** Profile data is tied to Django’s built-in user authentication.

---

## Authentication

**URLs:**
- **Login View:** `/login`
- **Logout View:** `/logout`
- **Register View:** `/register`
- **Password Reset View:** `/password-reset`

**Features:**
- **User Registration:** Allows new users to sign up with:
  - Username
  - Email
  - Password (hashed for security)
- **Login & Logout:** Users can securely log in and out of their accounts.
- **Password Reset:** Users can request a password reset via email.
- **Session Management:** Ensures users stay logged in until they manually log out.

---

## Additional Features and Enhancements

### Search Functionality
- Implemented search for **Merchandise Store**, **Wiki**, and **Profile**.
- Search queries match items, articles, and user profiles respectively.

### Responsive Design
- The app is designed to be mobile-friendly with a responsive UI.

### Admin Panel
- Django Admin provides management of:
  - Users
  - Products
  - Transactions
  - Wiki Articles
  - Comments
  - Profiles
