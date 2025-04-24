<h1 align="center">🏆 React-Achievements-Zustand 🏆</h1>

A flexible and customizable achievement system for React applications using Zustand for state management, perfect for adding gamification elements to your projects.

![React Achievements Demo](https://github.com/dave-b-b/react-achievements/blob/main/images/demo.gif?raw=true)

If you want to test the package, you can try it out here:

https://stackblitz.com/edit/vitejs-vite-rymmutrx

<h2 align="center">🚀 Installation</h2>

Install `react-achievements` using npm or yarn:

```bash
npm install react react-dom zustand react-achievements-zustand
```

or

```bash
yarn add react react-dom zustand react-achievements-zustand
```

<h2 align="center">🎮 Usage</h2>

Let's walk through setting up a simple RPG-style game with achievements using React-Achievements.

<h3 align="center">🛠 Set up the AchievementProvider</h3>

First, wrap your app or a part of your app with the AchievementProvider:

```jsx
import React from 'react';
import { AchievementProvider } from 'react-achievements-zustand';
import Game from './Game'; // Your main game component
import achievementConfig from './achievementConfig'; // Your achievement configuration

const initialState = {
    level: 1,
    experience: 0,
    monstersDefeated: 0,
    questsCompleted: 0,
    previouslyAwardedAchievements: ['first_step'], // Optional: Load previously awarded achievements
    // Add any other initial metrics here
};

function App() {
    return (
        <AchievementProvider
            config={achievementConfig} // Required: your achievement configuration
            initialState={initialState} // Required: initial game metrics and optionally previously awarded achievements. This can be loaded from your server
            storageKey="my-game-achievements" // Optional: customize local storage key
            badgesButtonPosition="top-right" // Optional: customize badges button position
            // Optional: add custom styles and icons here
        >
            <Game />
        </AchievementProvider>
    );
}

export default App;
```

<h3 align="center">🎣 Use the useAchievement hook</h3>

In your game components, use the useAchievement hook to update metrics and trigger achievement checks:
```jsx
import React, { useState } from 'react';
import { useAchievement } from 'react-achievements-zustand';

function Game() {
    const { setMetrics, metrics } = useAchievement();
    const [currentQuest, setCurrentQuest] = useState(null);

    const defeatMonster = () => {
        setMetrics((prevMetrics) => ({
            ...prevMetrics,
            monstersDefeated: prevMetrics.monstersDefeated + 1,
            experience: prevMetrics.experience + 10,
            level: Math.floor((prevMetrics.experience + 10) / 100) + 1, // Calculate new level
        }));
    };

    const completeQuest = () => {
        setMetrics((prevMetrics) => ({
            ...prevMetrics,
            questsCompleted: prevMetrics.questsCompleted + 1,
            experience: prevMetrics.experience + 50,
            level: Math.floor((prevMetrics.experience + 50) / 100) + 1, // Calculate new level
        }));
        setCurrentQuest(null);
    };

    const startQuest = () => {
        setCurrentQuest("Defeat the Dragon");
    };

    return (
        <div>
            <h1>My RPG Game</h1>
            <p>Level: {metrics.level}</p>
            <p>Experience: {metrics.experience}</p>
            <p>Monsters Defeated: {metrics.monstersDefeated}</p>
            <p>Quests Completed: {metrics.questsCompleted}</p>

            <div>
                <h2>Battle Arena</h2>
                <button onClick={defeatMonster}>Fight a Monster</button>
            </div>

            <div>
                <h2>Quest Board</h2>
                {currentQuest ? (
                    <>
                        <p>Current Quest: {currentQuest}</p>
                        <button onClick={completeQuest}>Complete Quest</button>
                    </>
                ) : (
                    <button onClick={startQuest}>Start a New Quest</button>
                )}
            </div>
        </div>
    );
}

export default Game;
```

<h2 align="center">✨ Features</h2>

- Flexible Achievement System: Define custom metrics and achievement conditions for your game or app.
- Built with TypeScript: Provides strong typing and improved developer experience.
- Zustand-Powered State Management: Leverages Zustand for predictable and scalable state management of achievements and metrics.
- Automatic Achievement Tracking: Achievements are automatically checked and unlocked when metrics change.
- Achievement Notifications: A modal pops up when an achievement is unlocked, perfect for rewarding players.
- Persistent Achievements: Unlocked achievements and metrics are stored in local storage, allowing players to keep their progress.
- Achievement Gallery: Players can view all their unlocked achievements, encouraging completionism.
- Confetti Effect: A celebratory confetti effect is displayed when an achievement is unlocked, adding to the excitement.
- Local Storage: Achievements are stored locally on the device.
- **Loading Previous Awards:** The AchievementProvider accepts an optional previouslyAwardedAchievements array in its initialState prop, allowing you to load achievements that the user has already earned.
- **Programmatic Reset:** Includes a `resetStorage` function accessible via the `useAchievementContext` hook to easily reset all achievement data.

<h2 align="center">🔧 API</h2>

<h3 align="center">🏗 AchievementProvider</h3>

#### Props:

- `config`: An object defining your metrics and achievements.
- `initialState`: The initial state of your metrics. Can also include an optional previouslyAwardedAchievements array of achievement IDs.
- `storageKey` (optional): A string to use as the key for localStorage. Default: 'react-achievements'
- `badgesButtonPosition` (optional): Position of the badges button. Default: 'top-right'
- `styles` (optional): Custom styles for the achievement components.

<h3 align="center">🪝 useAchievement Hook</h3>

#### Returns an object with:

- `updateMetrics`: Function to update the metrics. Accepts either a new metrics object or a function that receives the previous metrics and returns the new metrics.
- `unlockedAchievements`: Array of unlocked achievement IDs managed by Zustand.
- `resetStorage`: Function to clear all achievement data from local storage and reset the state.

<h3 align="center">🪝 useAchievementState Hook</h3>
<h4 align="center">Returns an object containing the current achievement state, useful for saving to a server or other persistent storage.
</h4>


#### Returns an object with:

- `metrics`: The current achievement metrics object.
- `previouslyAwardedAchievements`: An array of achievement IDs that have been previously awarded to the user.

**Example Usage:**

```jsx
import React from 'react';
import { useAchievementState } from 'react-achievements-zustand';

const SyncAchievementsButton = () => {
    const { metrics, previouslyAwardedAchievements } = useAchievementState();

    const handleSaveToServer = async () => {
        const achievementData = {
            metrics,
            previouslyAwardedAchievements,
        };
        try {
            const response = await fetch('/api/save-achievements', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(achievementData),
            });
            if (response.ok) {
                console.log('Achievement data saved successfully!');
            } else {
                console.error('Failed to save achievement data.');
            }
        } catch (error) {
            console.error('Error saving achievement data:', error);
        }
    };

    return (
        <button onClick={handleSaveToServer}>Save Achievements to Server</button>
    );
};

export default SyncAchievementsButton;
```

<h2 align="center">🎨 Customization</h2>

React-Achievements allows for extensive customization of its appearance. You can override the default styles by passing a `styles` prop to the `AchievementProvider`:

```jsx
const customStyles = {
  achievementModal: {
    // Custom styles for the achievement modal below
  },
  badgesModal: {
    // Custom styles for the badges modal below
  },
  badgesButton: {
    // Custom styles for the badges button below
  },
};

function App() {
  return (
    <AchievementProvider 
      config={achievementConfig} 
      initialState={initialState}
      styles={customStyles}
    >
      <Game />
    </AchievementProvider>
  );
}
```

### achievementModal (to be passed in as a customStyle above)

Customizes the modal that appears when an achievement is unlocked.

```
achievementModal: {
  overlay: {
    // Styles for the modal overlay (background)
    backgroundColor: 'rgba(0, 0, 0, 0.8)',
    // You can also customize other overlay properties like zIndex, transition, etc.
  },
  content: {
    // Styles for the modal content container
    backgroundColor: '#2a2a2a',
    color: '#ffffff',
    borderRadius: '10px',
    padding: '20px',
    // Add any other CSS properties for the content container
  },
  title: {
    // Styles for the achievement title
    fontSize: '24px',
    fontWeight: 'bold',
    color: '#ffd700',
  },
  icon: {
    // Styles for the achievement icon
    width: '64px',
    height: '64px',
    marginBottom: '10px',
  },
  description: {
    // Styles for the achievement description
    fontSize: '16px',
    marginTop: '10px',
  },
  button: {
    // Styles for the close button
    backgroundColor: '#4CAF50',
    color: 'white',
    padding: '10px 20px',
    border: 'none',
    borderRadius: '5px',
    cursor: 'pointer',
  },
}
```

### badgesModal (to be passed in as a customStyle above)

```
badgesModal: {
  overlay: {
    // Similar to achievementModal overlay
  },
  content: {
    // Similar to achievementModal content
  },
  title: {
    // Styles for the modal title
  },
  badgeContainer: {
    // Styles for the container holding all badges
    display: 'flex',
    flexWrap: 'wrap',
    justifyContent: 'center',
  },
  badge: {
    // Styles for individual badge containers
    margin: '10px',
    textAlign: 'center',
  },
  badgeIcon: {
    // Styles for badge icons
    width: '50px',
    height: '50px',
  },
  badgeTitle: {
    // Styles for badge titles
    fontSize: '14px',
    marginTop: '5px',
  },
  button: {
    // Styles for the close button (similar to achievementModal button)
  },
}
```


### badgesButton (to be passed in as a customStyle above)

```
badgesButton: {
  // Styles for the floating badges button
  position: 'fixed',
  padding: '10px 20px',
  backgroundColor: '#007bff',
  color: '#ffffff',
  border: 'none',
  borderRadius: '5px',
  cursor: 'pointer',
  zIndex: 1000,
  // You can add more CSS properties as needed. These are just regular CSS
}

```

<h2 align="center">Resetting React Achievements</h2>

The achievements and metrics are managed by Zustand and persisted in local storage. You have two primary ways to reset the achievement system:

1.  **Programmatic Reset:** Use the `resetStorage` function provided by the `useAchievementContext` hook within your components:

```jsx
    import React from 'react';
    import { useAchievementContext } from 'react-achievements-zustand';

    function ResetButton() {
      const { resetStorage } = useAchievementContext();

      const handleReset = () => {
        resetStorage();
        console.log('Achievements and progress reset!');
      };

      return <button onClick={handleReset}>Reset Achievements</button>;
    }
```

<h2 align="center">💾 Saving and Loading Progress</h2>

s<h4 align="center">To persist user achievement progress across sessions or devices, you'll typically want to save the `metrics` and `previouslyAwardedAchievements` from your Zustand store to your server. You can use the `useAchievementState` hook to access this data and trigger the save operation, for example, when the user logs out:
</h4>

```jsx
import React from 'react';
import { useAchievementState } from 'react-achievements-zustand/hooks/useAchievementState';

const LogoutButtonWithSave = ({ onLogout }) => {
    const { metrics, previouslyAwardedAchievements } = useAchievementState();

    const handleLogoutAndSave = async () => {
        const achievementData = {
            metrics,
            previouslyAwardedAchievements,
        };
        try {
            const response = await fetch('/api/save-achievements', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    // Include any necessary authentication headers
                },
                body: JSON.stringify(achievementData),
            });
            if (response.ok) {
                console.log('Achievement data saved successfully before logout!');
            } else {
                console.error('Failed to save achievement data before logout.');
            }
        } catch (error) {
            console.error('Error saving achievement data:', error);
        } finally {
            // Proceed with the logout action regardless of save success
            onLogout();
        }
    };

    return (
        <button onClick={handleLogoutAndSave}>Logout</button>
    );
};

export default LogoutButtonWithSave;
```

<h2 align="center">🏆Available Icons🏆</h2>

```
// General Progress & Milestones
    levelUp: '🏆',
    questComplete: '📜',
    monsterDefeated: '⚔️',
    itemCollected: '📦',
    challengeCompleted: '🏁',
    milestoneReached: '🏅',
    firstStep: '👣',
    newBeginnings: '🌱',
    breakthrough: '💡',
    growth: '📈',

    // Social & Engagement
    shared: '🔗',
    liked: '❤️',
    commented: '💬',
    followed: '👥',
    invited: '🤝',
    communityMember: '🏘️',
    supporter: '🌟',
    connected: '🌐',
    participant: '🙋',
    influencer: '📣',

    // Time & Activity
    activeDay: '☀️',
    activeWeek: '📅',
    activeMonth: '🗓️',
    earlyBird: '⏰',
    nightOwl: '🌙',
    streak: '🔥',
    dedicated: '⏳',
    punctual: '⏱️',
    consistent: '🔄',
    marathon: '🏃',

    // Creativity & Skill
    artist: '🎨',
    writer: '✍️',
    innovator: '🔬',
    creator: '🛠️',
    expert: '🎓',
    master: '👑',
    pioneer: '🚀',
    performer: '🎭',
    thinker: '🧠',
    explorer: '🗺️',

    // Achievement Types
    bronze: '🥉',
    silver: '🥈',
    gold: '🥇',
    diamond: '💎',
    legendary: '✨',
    epic: '💥',
    rare: '🔮',
    common: '🔘',
    special: '🎁',
    hidden: '❓',

    // Numbers & Counters
    one: '1️⃣',
    ten: '🔟',
    hundred: '💯',
    thousand: '🔢',

    // Actions & Interactions
    clicked: '🖱️',
    used: '🔑',
    found: '🔍',
    built: '🧱',
    solved: '🧩',
    discovered: '🔭',
    unlocked: '🔓',
    upgraded: '⬆️',
    repaired: '🔧',
    defended: '🛡️',

    // Placeholders
    default: '⭐', // A fallback icon
    loading: '⏳',
    error: '⚠️',
    success: '✅',
    failure: '❌',

    // Miscellaneous
    trophy: '🏆',
    star: '⭐',
    flag: '🚩',
    puzzle: '🧩',
    gem: '💎',
    crown: '👑',
    medal: '🏅',
    ribbon: '🎗️',
    badge: '🎖️',
    shield: '🛡️',
```

<h2 align="center">📄 License</h2>
MIT

React-Achievements provides a comprehensive achievement system for React applications, perfect for adding gamification elements to your projects. Whether you're building a game, an educational app, or any interactive experience, this package offers an easy way to implement and manage achievements, enhancing user engagement and retention.

<h2 align="center">📥 Exporting and Importing Achievement State</h2>

You can export the current state of achievements to save it to your server and later import it back. Here's how:

### Exporting the State

To export the current state, use the `useAchievementStore` hook directly:

```jsx
import { useAchievementStore } from 'react-achievements-zustand';

const ExportStateButton = () => {
    const store = useAchievementStore();
    
    const handleExport = () => {
        // Get the complete state
        const state = {
            metrics: store.metrics,
            unlockedAchievements: store.unlockedAchievements,
            previouslyAwardedAchievements: store.previouslyAwardedAchievements,
            notifications: store.notifications,
            isInitialized: store.isInitialized
        };
        
        // You can now save this state to your server
        saveToServer(state);
    };

    return <button onClick={handleExport}>Export Achievement State</button>;
};
```

### Importing the State

To import a previously saved state, provide it in the `initialState` prop of the `AchievementProvider`:

```jsx
import { AchievementProvider } from 'react-achievements-zustand';

// This could be loaded from your server
const savedState = {
    metrics: {
        level: 5,
        experience: 1000,
        // ... other metrics
    },
    previouslyAwardedAchievements: ['achievement1', 'achievement2'],
    // You can include other state properties if needed
};

function App() {
    return (
        <AchievementProvider
            config={achievementConfig}
            initialState={savedState}
            storageKey="my-game-achievements"
        >
            <Game />
        </AchievementProvider>
    );
}
```

### Best Practices

1. When exporting state, consider what data you need to persist:
   - `metrics`: Required for tracking progress
   - `unlockedAchievements`: Required for maintaining achievement status. This is used to trigger achievements as they are earned
   - `previouslyAwardedAchievements`: Required for historical records
   - `notifications`: Optional, typically not needed to persist
   - `isInitialized`: Optional, will be set automatically on provider mount

2. When importing state, the minimum required properties in `initialState` are:
   - `metrics`: Your achievement metrics
   - `previouslyAwardedAchievements`: Previously awarded achievements

3. Consider implementing automatic state sync:

```jsx
import { useAchievementStore } from 'react-achievements-zustand';
import { useEffect } from 'react';

const AutoSyncAchievements = () => {
    const store = useAchievementStore();

    useEffect(() => {
        // Set up an interval to sync state periodically
        const syncInterval = setInterval(() => {
            const stateToSync = {
                metrics: store.metrics,
                unlockedAchievements: store.unlockedAchievements,
                previouslyAwardedAchievements: store.previouslyAwardedAchievements
            };
            
            // Your sync logic here
            saveToServer(stateToSync);
        }, 5 * 60 * 1000); // Every 5 minutes

        return () => clearInterval(syncInterval);
    }, [store]);

    return null; // This is a utility component, it doesn't render anything
};
```
