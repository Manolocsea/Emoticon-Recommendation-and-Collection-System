
# Emoticon Recommendation and Collection System

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Installation and Setup](#installation-and-setup)
- [Project Structure](#project-structure)
- [Features](#features)
  - [Navigation Bar](#navigation-bar)
  - [Recommand Tab](#recommand-tab)
  - [Favorites Tab](#favorites-tab)
  - [Account Tab](#account-tab)
- [Technical Details](#technical-details)
- [Visual Design and User Interaction](#visual-design-and-user-interaction)
- [Contributing](#contributing)
- [License](#license)

## Introduction

This Vue.js application serves as an interactive image gallery, providing functionalities to sort and filter images based on multiple criteria, manage favorites, and handle user accounts.

## Prerequisites

- Node.js (>= 12.x)
- npm (>= 6.x)
- Visual Studio Code or any other code editor (optional but recommended)

## Installation and Setup

### 1. Clone the Repository

Open your command prompt (cmd) and run the following command to clone the repository:

\`\`\`bash
git clone https://github.com/Manolocsea/Emoticon-Recommendation-and-Collection-System.git....（Since I haven't uploaded to github yet there is only one README file, you can unzip my project folder）(this is project zip link:https://pan.baidu.com/s/1ObUeXOHYQqH4NbzlgBVG-Q?pwd=qdkd )
\`\`\`

### 2. Navigate to the Project Directory

\`\`\`bash
cd vuejs-image-gallery
\`\`\`

### 3. Install Dependencies

Run the following command to install the project dependencies:

\`\`\`bash
npm install
\`\`\`

### 4. Install Element UI

To install Element UI, run the following command:

\`\`\`bash
npm install element-ui --save
\`\`\`

You may also need to add Element UI to your main.js:

\`\`\`javascript
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
\`\`\`

### 5. Start the Development Server

Run the following command to start the development server:

\`\`\`bash
npm run serve
\`\`\`

Your application should now be running on `http://localhost:8080`.

## Project Structure

- `src/`: Contains the main Vue.js application code.
  - `components/`: Vue.js components.
  - `assets/`: Static assets like images and styles.
  - `App.vue`: The main Vue.js component.
  - `main.js`: Entry point for the Vue.js application.
- `public/`: Contains the index.html file.
- `package.json`: Lists the package dependencies for the project.

## Features

### Navigation Bar

- Horizontal navigation for switching between "Recommand", "Favorites", and "Account".

### Recommand Tab

- Custom radio buttons for filtering images based on categories.
- Provides sub-options and sub-sub-options for more detailed filtering.
- Allows users to mark or unmark images as favorites.

### Favorites Tab

- Displays a gallery of favorite images.
- Requires login for viewing favorites.

### Account Tab

- Login and logout functionality.
- Editable profile information when logged in.

## Technical Details

- Uses Vue.js lifecycle hooks for initializing data and handling events.
- Implements watchers and computed properties for reactive data manipulation.
- Employs methods for handling user interactions like login, image marking, and profile editing.

## Visual Design and User Interaction

The project employs a modern and responsive design, made possible through a well-structured CSS stylesheet. Here are some highlights:

### Navigation Bar

- Positioned at the top and spans the entire width of the window.
- Fixed position ensures constant accessibility.

### Content Area

- Centralized layout for better focus and readability.
- Dynamically positioned based on other active elements.

### Radio Buttons and Options

- Custom-designed radio buttons for better user experience.
- Active selections are highlighted for better visual feedback.

### Error Messages

- Errors (like login errors) are displayed in red for better visibility.

### Image Gallery

- Images are displayed in a grid-like structure.
- Uses absolute positioning for dynamic layout.
- Marked or selected images have a different background color for better visual feedback.

## Contributing

If you would like to contribute, please fork the repository and submit your changes as a pull request.

## License

This project is licensed under the MIT License.
