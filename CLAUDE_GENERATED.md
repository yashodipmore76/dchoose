# Claude Generated Code

**Task**: Description:

Add modern features to make the app production-ready and scalable.

🆕 Suggested Features:

Theme Toggle: Light/Dark mode using Tailwind or CSS variables

Search & Filter: Enable user to filter categories or options dynamically

Persistent State: Use localStorage or sessionStorage to save user preferences or selections

Basic Analytics: Track user interaction with a lightweight tool (like react-ga or custom logging)

✅ Deliverables:

Each feature behind a toggle/optional flag

Documented in README.md

Tests (unit/integration) if possible for newly added utilities/components

**Generated on**: Wed Jul 30 02:25:13 UTC 2025

## Generated Content

```
To implement the suggested features in the Ionic React TypeScript application for government billing/invoicing, we can follow these steps:

**FILES_TO_CREATE:**
1. `src/utils/themeContext.tsx` - Context for managing light/dark mode
2. `src/components/ThemeToggle.tsx` - Component for toggling between light and dark mode
3. `src/utils/storageUtils.ts` - Utility functions for working with localStorage/sessionStorage
4. `src/utils/analyticsUtils.ts` - Utility functions for tracking user interactions (optional)
5. `src/components/SearchFilter.tsx` - Component for searching and filtering categories or options

**FILES_TO_MODIFY:**
1. `src/App.tsx`
2. `README.md`

**CODE_CHANGES:**

**1. `src/utils/themeContext.tsx`**
```typescript
import React, { createContext, useState, useEffect } from 'react';
import { Preferences } from '@capacitor/preferences';

interface ThemeContextData {
  isDarkMode: boolean;
  toggleTheme: () => void;
}

export const ThemeContext = createContext<ThemeContextData>({
  isDarkMode: false,
  toggleTheme: () => {},
});

export const ThemeProvider: React.FC = ({ children }) => {
  const [isDarkMode, setIsDarkMode] = useState<boolean>(false);

  useEffect(() => {
    const loadThemePreference = async () => {
      const { value } = await Preferences.get({ key: 'theme' });
      setIsDarkMode(value === 'dark');
    };
    loadThemePreference();
  }, []);

  const toggleTheme = async () => {
    const newTheme = !isDarkMode ? 'dark' : 'light';
    setIsDarkMode(!isDarkMode);
    await Preferences.set({ key: 'theme', value: newTheme });
  };

  return (
    <ThemeContext.Provider value={{ isDarkMode, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};
```

**2. `src/components/ThemeToggle.tsx`**
```typescript
import React, { useContext } from 'react';
import { IonToggle, IonLabel } from '@ionic/react';
import { ThemeContext } from '../utils/themeContext';

const ThemeToggle: React.FC = () => {
  const { isDarkMode, toggleTheme } = useContext(ThemeContext);

  return (
    <div className="flex items-center">
      <IonLabel>Light</IonLabel>
      <IonToggle
        checked={isDarkMode}
        onIonChange={toggleTheme}
        className="mx-2"
      />
      <IonLabel>Dark</IonLabel>
    </div>
  );
};

export default ThemeToggle;
```

**3. `src/utils/storageUtils.ts`**
```typescript
export const setItemInStorage = (key: string, value: any, useSessionStorage = false) => {
  const storage = useSessionStorage ? sessionStorage : localStorage;
  storage.setItem(key, JSON.stringify(value));
};

export const getItemFromStorage = (key: string, useSessionStorage = false) => {
  const storage = useSessionStorage ? sessionStorage : localStorage;
  const item = storage.getItem(key);
  return item ? JSON.parse(item) : null;
};

export const removeItemFromStorage = (key: string, useSessionStorage = false) => {
  const storage = useSessionStorage ? sessionStorage : localStorage;
  storage.removeItem(key);
};
```

**4. `src/utils/analyticsUtils.ts` (optional)**
```typescript
import ReactGA from 'react-ga';

export const initializeAnalytics = () => {
  ReactGA.initialize('YOUR_TRACKING_ID');
};

export const trackPageView = (path: string) => {
  ReactGA.pageview(path);
};

export const trackEvent = (category: string, action: string, label?: string) => {
  ReactGA.event({
    category,
    action,
    label,
  });
};
```

**5. `src/components/SearchFilter.tsx`**
```typescript
import React, { useState } from 'react';
import { IonSearchbar, IonList, IonItem, IonLabel } from '@ionic/react';

interface SearchFilterProps {
  options: string[];
  onOptionSelect: (option: string) => void;
}

const SearchFilter: React.FC<SearchFilterProps> = ({ options, onOptionSelect }) => {
  const [searchText, setSearchText] = useState('');
  const [filteredOptions, setFilteredOptions] = useState(options);

  const handleSearch = (event: CustomEvent) => {
    const query = event.detail.value ?? '';
    setSearchText(query);
    setFilteredOptions(options.filter((option) => option.toLowerCase().includes(query.toLowerCase())));
  };

  return (
    <div>
      <IonSearchbar value={searchText} onIonChange={handleSearch} placeholder="Search options" />
      <IonList>
        {filteredOptions.map((option) => (
          <IonItem key={option} button onClick={() => onOptionSelect(option)}>
            <IonLabel>{option}</IonLabel>
          </IonItem>
        ))}
      </IonList>
    </div>
  );
};

export default SearchFilter;
```

**6. `src/App.tsx`**
```typescript
import React from 'react';
import { Redirect, Route } from 'react-router-dom';
import { IonApp, IonRouterOutlet, setupIonicReact } from '@ionic/react';
import { IonReactRouter } from '@ionic/react-router';

import ThemeToggle from './components/ThemeToggle';
import SearchFilter from './components/SearchFilter';
import { ThemeProvider } from './utils/themeContext';
import { initializeAnalytics } from './utils/analyticsUtils'; // Optional

/* Core CSS required for Ionic components to work properly */
import '@ionic/react/css/core.css';

/* Basic CSS for apps built with Ionic */
import '@ionic/react/css/normalize.css';
import '@ionic/react/css/structure.css';
import '@ionic/react/css/typography.css';

/* Optional CSS utils that can be commented out */
import '@ionic/react/css/padding.css';
import '@ionic/react/css/float-elements.css';
import '@ionic/react/css/text-alignment.css';
import '@ionic/react/css/text-transformation.css';
import '@ionic/react/css/flex-utils.css';
import '@ionic/react/css/display.css';

/* Theme variables */
import './theme/variables.css';

setupIonicReact();

// Initialize analytics (optional)
initializeAnalytics();

const App: React.FC = () => (
  <IonApp>
    <ThemeProvider>
      <IonReactRouter>
        <IonRouterOutlet>
          <Route path="/home" component={HomePage} exact={true} />
          <Route exact path="/">
            <Redirect to="/home" />
          </Route>
        </IonRouterOutlet>
      </IonReactRouter>
    </ThemeProvider>
  </IonApp>
);

const HomePage: React.FC = () => {
  const [selectedOptions, setSelectedOptions] = useState<string[]>([]);

  const handleOptionSelect = (option: string) => {
    setSelectedOptions([...selectedOptions, option]);
  };

  return (
    <div className="p-4">
      <ThemeToggle />
      <SearchFilter options={['Option 1', 'Option 2', 'Option 3']} onOptionSelect={handleOptionSelect} />
      <div>
        <h2>Selected Options:</h2>
        <ul>
          {selectedOptions.map((option) => (
            <li key={option}>{option}</li>
          ))}
        </ul>
      </div>
    </div>
  );
};

export default App;
```

**7. `README.md`**
Update the `README.md` file with information about the new features and how to use them.

**INSTRUCTIONS:**
1. Install the required dependencies:
```bash
npm install @capacitor/preferences react-ga
```

2. Configure the analytics tracking ID (optional) in `src/utils/analyticsUtils.ts`.

3. Import the theme styles in `src/theme/variables.css`:
```css
/* Theme styles */
:root {
  /* Light mode styles */
  --ion-color-primary: #3880ff;
  --ion-color-primary-rgb: 56, 128, 255;
  --ion-color-primary-contrast: #ffffff;
  --ion-color-primary-contrast-rgb: 255, 255, 255;
  --ion-color-primary-shade: #3171e0;
  --ion-color-primary-tint: #4c8dff;

  /* Dark mode styles */
  --ion-color-dark: #222428;
  --ion-color-dark-rgb: 34, 36, 40;
  --ion-color-dark-contrast: #ffffff;
  --ion-color-dark-contrast-rgb: 255, 255, 255;
  --ion-color-dark-shade: #1e2023;
  --ion-color-dark-tint: #383a3e;
}

.dark-mode {
  --ion-background-color: var(--ion-color-dark);
  --ion-text-color: var(--ion-color-dark-contrast);
}
```

4. In `src/App.tsx`, use the `ThemeContext` to apply the `dark-mode` class to the root element when dark mode is enabled:
```typescript
const App: React.FC = () => {
  const { isDarkMode } = useContext(ThemeContext);

  return (
    <IonApp className={isDarkMode ? 'dark-mode' : ''}>
      {/* ... */}
    </IonApp>
  );
};
```

5. Import the new components and utilities in the appropriate places within your application.

6. Implement user preferences and interactions tracking as per your requirements.

By following these steps, you should be able to add the suggested features to your Ionic React TypeScript application for government billing/invoicing, making it more user-friendly and production-ready.
```
