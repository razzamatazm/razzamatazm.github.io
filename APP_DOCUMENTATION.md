# Rainbow Words Trainer - Technical Documentation

## Overview
A progressive sight words learning application with 8 color-coded levels (Red through Light Blue). Students master words through study, review, and testing phases to earn badges. Features intelligent word selection, cross-level retention, and backup/restore capabilities.

## Core Architecture

### File Structure
- `index.html` - Single-file application containing all HTML, CSS, and JavaScript
- `CNAME` - GitHub Pages custom domain configuration
- `README.md` - User-facing project information
- `APP_DOCUMENTATION.md` - Technical documentation (this file)

### Technology Stack
- **Frontend**: Vanilla JavaScript, HTML5, CSS3
- **Storage**: LocalStorage API
- **Animations**: CSS keyframes and transitions
- **Mobile**: Web Share API, Clipboard API
- **Hosting**: GitHub Pages (static site)

## State Management

### Storage Key
`'sightwords_rainbow_v3'` in localStorage

### State Structure
```javascript
{
  perLevel: [
    {
      words: [
        {
          text: 'the',
          seen: 5,
          correct: 3,
          wrong: 1,
          streak: 2,
          mastered: false,
          tricky: true,
          lastSeenTimestamp: Date,
          masteryDates: [Date, Date],
          sessionsSeen: 2,
          currentSessionSeen: false
        }
      ],
      badgeEarned: false
    }
  ],
  levelIdx: 0,
  phase: 'study',
  studentName: 'Student',
  reviewMode: 'current',
  reviewStreak: 10,
  seenTotal: 25,
  currentSessionId: 'uuid',
  // ... additional state fields
}
```

### Key State Variables
- `levelIdx`: Current level (0-7)
- `phase`: 'study' | 'review' | 'final' | 'prefinal' | 'postreview'
- `reviewMode`: 'current' | 'general'
- `reviewStreak`: Streak counter for general review mode (0-22)

## Level System

### 8 Rainbow Levels
```javascript
const LEVELS = [
  { name:'Red', color:'#ef4444', badge:'🍓', words:[...12 words] },
  { name:'Orange', color:'#f97316', badge:'🍊', words:[...10 words] },
  { name:'Yellow', color:'#eab308', badge:'🌻', words:[...13 words] },
  { name:'Green', color:'#22c55e', badge:'🍀', words:[...12 words] },
  { name:'Blue', color:'#3b82f6', badge:'💙', words:[...11 words] },
  { name:'Purple', color:'#a855f7', badge:'🍇', words:[...14 words] },
  { name:'Pink', color:'#ec4899', badge:'🌸', words:[...10 words] },
  { name:'Light Blue', color:'#38bdf8', badge:'🦋', words:[...13 words] }
];
```

## User Flow

### Initial Load

#### New User
1. Shows welcome modal with app overview
2. Explains Level Mode vs Review Mode
3. Provides quick tips (tap zones, keyboard shortcuts, profile access)
4. User enters name and clicks "Let's Start!"
5. Shows level picker to choose starting level

#### Returning User
1. Shows "Welcome back, [Name]!" modal
2. Displays three options:
   - **Continue Current Level**: Shows current level with progress
   - **Choose Level**: Opens level picker
   - ~~**Review All Known Words**: Switches to general review~~ (removed - now in level picker)
3. Auto-suggests lowest unpassed level

### Study Phase

#### Word Selection Logic
Uses `pickFromUnifiedPool()` with two-tier system:

**TIER 1: Critical Reviews** (highest priority)
- Words not seen in 5+ days from any level
- Ensures long-term retention

**TIER 2: Weighted Selection**
- **Current Level Words**: Base weight 25 + time/tricky bonuses
- **Previous Level Tricky Words**: Base weight 12 × recency multiplier
- **Previous Level Mastered Words**: Base weight 5-7 × recency multiplier
- **Recency Multiplier**: Recent levels weighted higher (1.0 → 0.6 → 0.4...)

#### Target Distribution (Current Level Mode)
- **70%** current level words
- **15%** tricky words from previous levels
- **15%** mastered words from previous levels
- **(80/20 if no tricky words)**

#### Cross-Level Review
- Always includes mastered and tricky words from previous levels
- No day threshold - works same-day
- Weighted by level distance (recent levels prioritized)

### Review Phase

#### Trigger
When ALL words in level are mastered (100% mastery required)

#### Process
1. **First Review**: Each word appears once
2. **If any missed**:
   - Shows score modal
   - Restarts review with sequence:
     - Missed words (1x at beginning)
     - Correct words (1x in middle)
     - Missed words (1x at end)
   - Marks missed words as tricky
3. **Repeat until 100% correct**
4. **Then offer**: Review again or take test

### Test Phase

#### Trigger
- After successful review, OR
- Direct selection from modal

#### Process
- Each word appears once in random order
- No second chances during test

#### Results
- **100%**: Badge earned! Confetti celebration + "Next Level" + "Choose Level"
- **<100%**: Shows score, missed words, offers "Quick Review" or "Test" again

## Mastery System

### Requirements (ALL must be met)
1. **Streak**: 2 correct answers in a row
2. **Sessions**: Word seen in 1+ session
3. **Time Span**: 0 days (same-day mastery allowed)
4. **Mastery Dates**: At least 2 recorded correct answers

### Constants
```javascript
MASTERY_STREAK_REQUIRED = 2      // 2 correct in a row
MASTERY_SESSIONS_REQUIRED = 1    // 1 session minimum
MASTERY_TIME_SPAN_DAYS = 0       // Same-day mastery allowed
```

### Tricky Word System

#### Becomes Tricky When:
1. Gets wrong **2+ times total** during practice
2. Missed during a review session
3. Missed during a test

#### Stops Being Tricky When:
- Gets correct **5 times in a row**

#### Constants
```javascript
TRICKY_ADD_AFTER_WRONGS = 2      // 2 misses → tricky
TRICKY_REMOVE_AFTER_STREAK = 5   // 5 streak → remove tricky
```

#### Behavior
- Tricky words get higher weight in selection
- Always included in cross-level review
- Don't block test eligibility (only current level mastery counts)

## UI Components

### Header

#### Profile Button (Top)
- Student name with 👤 icon
- Badge belt showing earned badges (28px emoji size)
- Clickable to open profile modal

#### Level Button (Bottom)
- Colored dot + level name + mode
- **Study Mode**: "🔴 Red Level Study Mode"
- **Review Mode**: "🌈 All Known Words Review" (rainbow animated dot)
- "Click to Change Level" hint below
- Opens level picker on click

### Main Area
- **Large Word Display**: Current word in large, readable font
- **Tap Zones**:
  - Left (red): "Missed it"
  - Right (green): "Got it!"
- **Keyboard Shortcuts**: W = wrong, R = right

### Footer

#### Progress Section
- **Progress Bar**: 30px tall, gradient fill
- **Label**: 16px bold text
  - Study: "Level Progress"
  - Review: "Review Progress"
  - Test: "Test Progress"

#### Stats Section
- **Learned Stat**: "Red Words Learned: 5/12" (16px bold, left-aligned)
- **Reset Level Button**: Right-aligned, subtle styling (hidden in All Known Words mode)

#### Streak Counter (All Known Words Mode Only)
- **Label**: "Streak:" text
- **Visual Bar**: 20 segments
- **Effects**:
  - 0-19: Green fill
  - 20-21: Yellow glow effect
  - 22+: Rainbow chase effect (each segment cycles through rainbow colors)
- **Behavior**: Starts at 10, increases on correct, decreases by 1 on wrong

### Modals

#### Welcome Modal
- App overview with Level Mode and Review Mode explanations
- Quick tips section
- Name input field
- "Let's Start!" button
- "📥 Import Backup" button (ghost style)

#### Welcome Back Modal
- Personalized greeting
- Three options (styled as large cards):
  - Continue current level (with progress)
  - Choose level
  - ~~Review all known words~~ (removed - now in level picker)

#### Level Picker Modal
- **3×3 Grid Layout**: 8 color levels + All Known Words
- Each button shows:
  - Colored dot
  - Level name
  - Word count
- **All Known Words** (9th option):
  - Rainbow animated dot
  - Shows count of all mastered words
  - Switches to general review mode

#### Pre-Final Modal (Review/Test Options)
- Shown when all words mastered
- Two options:
  - "Practice": Review all words once
  - "Test Again": Take the final test
- Message varies if badge already earned

#### Review Failed Modal
- Shows score (e.g., "11/12 correct")
- "Let's Try Again!" message
- "Start Review" button
- Restarts review with missed word priority

#### Test Results Modal
- Shows score and percentage
- Lists missed words
- Two buttons:
  - "Quick Review": Return to study
  - "Test": Retake test (always enabled)

#### Success Modal (Badge Earned)
- Trophy icon
- Badge emoji (large)
- Celebration message
- Confetti animation (480 pieces!)
- Two options:
  - "Next Level": Advance to next level
  - "Choose a Level": Open level picker

#### Profile Modal
- **× Close Button**: Top-right corner
- **Profile Header**: Avatar, name, "Sight Words Champion"
- **Stats Grid** (4 cards):
  - Words Mastered
  - Tricky Words
  - Words Seen
  - Completion %
- **Badge Collection**: Grid of all badges (earned/locked)
- **Tricky Word Collection**: Red-themed cards showing current tricky words
- **Mastered Words List**: Green-themed scrollable grid of all mastered words
- **Backup & Restore Section**:
  - "📤 Export Profile" button
  - "📥 Import Profile" button
- **Reset All Progress**: Red button at bottom

## Key Functions

### Core Flow Functions
- `boot()`: Initializes app, shows welcome or welcome back
- `nextWord()`: Main word selection and display logic
- `markRight()` / `markWrong()`: Handle user responses
- `updateHeader()`: Updates level button and profile display
- `updateProgressBars()`: Updates progress bar and learned stat

### Word Selection
- `pickFromUnifiedPool()`: Advanced weighted selection algorithm
- `getAllWordsForSelection()`: Builds pool of available words
- `getCriticalWords()`: Finds words not seen in 5+ days
- `computeWeight()`: Calculates selection weight for each word
- `hasTrickyWordsInPool()`: Checks for tricky words to adjust distribution

### Mastery & Progress
- `checkMastery()`: Determines if word meets mastery criteria
- `allMasteredInLevel()`: Checks if all current level words mastered
- `masteredCount()`: Counts mastered words in current level
- `calculateTotalStats()`: Computes stats across all levels

### Review & Test
- `openPreFinal()`: Shows review/test options modal
- `endReviewSessionAndShowResults()`: Processes review results
- `startReviewWithMissedWordsPriority()`: Builds focused review queue
- `endTestAndShowResults()`: Processes test results
- `showBadgeCelebration()`: Confetti animation

### UI Updates
- `updateBadges()`: Updates badge belt display
- `updateStreakCounter()`: Updates streak bar (general review mode)
- `populateProfileBadges()`: Fills badge collection
- `populateTrickyWords()`: Displays tricky word cards
- `populateMasteredWords()`: Lists all mastered words

### Level Management
- `switchLevel()`: Changes to new level, resets state
- `openLevelPicker()`: Shows level selection modal
- `populateLevelGrid()`: Builds 3×3 level grid with All Known Words option
- `syncWordsWithConfig()`: Ensures level words match configuration

### Import/Export
- `exportProgress()`: Creates JSON backup (download or share)
- `importProgress()`: Shows import modal
- `processImportData()`: Validates and applies imported data
- `fallbackExport()`: Handles clipboard/download fallback
- `downloadFile()`: Creates download link for backup file

## Learning Science

### Weighted Selection Algorithm

#### Current Level Mode
```
Current Level Word Weight:
  base = 1 + (wrong × 4) - streak
  base += 25 (current level boost)
  base += timeBoost (days since seen × 0.8, max 10)
  if tricky: base += 8
  
Previous Level Word Weight:
  recencyMultiplier = max(0.2, 1 - (levelDistance × 0.2))
  
  If tricky:
    base += 12 × recencyMultiplier
    base += timeBoost × recencyMultiplier
  
  If mastered:
    base += (hasTrickyWords ? 5 : 7) × recencyMultiplier
    base += timeBoost × recencyMultiplier
```

#### General Review Mode
- All mastered words from all levels
- Enhanced tricky word priority (+5 boost)
- Time-based boost for words not seen recently
- No current level preference

### Spaced Repetition
- **Critical threshold**: 5 days (words not seen get highest priority)
- **Review threshold**: 2 days (for tricky words)
- **History blocking**: Prevents same word appearing too frequently
- **Retry delay**: 3 words between retry attempts

### Session Tracking
- Each session gets unique ID
- Tracks which words seen in current session
- Prevents duplicate counting
- Resets on level switch or app reload

## UI Design Patterns

### Color System
```css
--bg: #0f1115 (dark background)
--panel: #1a1d24 (card background)
--fg: #e5e7eb (text)
--muted: #9ca3af (secondary text)
--accent: #6ea8fe (blue accent)
--success: #2effaa (green success)
```

### Rainbow Colors (in order)
1. Red: #ef4444
2. Orange: #f97316
3. Yellow: #eab308
4. Green: #22c55e
5. Blue: #3b82f6
6. Purple: #a855f7
7. Pink: #ec4899
8. Cyan: #38bdf8

### Responsive Breakpoints
- Mobile: < 768px
- Desktop: ≥ 768px

### Animation Effects
- **Confetti**: 480 pieces on badge earn
- **Badge Celebration**: 3-second display with confetti
- **Streak Glow**: Pulsing glow at streak 20+
- **Rainbow Chase**: Color cycling at streak 22+
- **Rainbow Dot**: Animated gradient for All Known Words mode

## Import/Export System

### Export Format
```json
{
  "version": "1.0",
  "exportDate": "2025-10-08T12:00:00.000Z",
  "studentName": "Student Name",
  "data": { /* full state object */ }
}
```

### Export Behavior
- **Mobile**: Tries native share sheet first, falls back to clipboard
- **Desktop**: Downloads JSON file
- **Filename**: `rainbow-words-{studentName}-{date}.json`

### Import Behavior
1. User selects file or pastes JSON
2. Validates data structure
3. Shows confirmation modal with student name and export date
4. Warns about replacing current progress
5. On confirm: Replaces all state and refreshes UI

### Mobile Optimizations
- Web Share API for native sharing
- Clipboard API for easy copy/paste
- File input for backup file selection
- Touch-friendly button sizes

## Recent Updates & Changes

### Major Features (Latest First)
1. **Import/Export System**: Full backup and restore with mobile support
2. **UI Redesign**: Stacked, centered header with profile and level buttons
3. **3×3 Level Picker**: All Known Words as 9th option
4. **Rainbow Chase Effect**: Animated rainbow at streak 22+
5. **Improved Onboarding**: Better explanations and tips
6. **Cross-Level Review**: 70/15/15 distribution with recency weighting
7. **Profile Enhancements**: Tricky words and mastered words sections
8. **Simplified Footer**: Combined learned/remaining, removed clutter
9. **Mode Integration**: Mode shown in level button, removed separate chips
10. **Review Retry Logic**: Missed words prioritized in review retries

### Bug Fixes (Latest First)
1. Fixed welcome back continue button crash
2. Fixed mode switching from All Known Words
3. Fixed phaseBadge null reference errors
4. Fixed reviewModeBtn event listener errors
5. Fixed test button disabled after failed test
6. Fixed progress bar not updating before prefinal modal
7. Fixed same-day cross-level word practice
8. Fixed loading screen on boot
9. Fixed modal state conflicts
10. Fixed button ID conflicts

### UI Improvements (Latest First)
1. Moved Reset All to profile modal (red styling)
2. Moved Reset Level to footer (right-aligned, hidden in review mode)
3. Increased progress bar height to 30px (2.5x)
4. Increased progress labels to 16px bold
5. Increased badge emoji size to 28px
6. Left-aligned learned stat with color name
7. Removed floating tooltip
8. Profile close button as × in top-right
9. Stacked and centered header boxes
10. Combined level text into single span

## Constants & Configuration

### Mastery Rules
```javascript
MASTERY_STREAK_REQUIRED = 2      // 2 correct in a row
MASTERY_SESSIONS_REQUIRED = 1    // 1 session minimum
MASTERY_TIME_SPAN_DAYS = 0       // Same-day mastery allowed
```

### Tricky Word Rules
```javascript
TRICKY_ADD_AFTER_WRONGS = 2      // 2 misses → tricky
TRICKY_REMOVE_AFTER_STREAK = 5   // 5 streak → remove tricky
```

### Selection Weights
```javascript
WRONG_FACTOR = 4                 // Weight multiplier for wrong answers
MIN_WEIGHT = 1                   // Minimum selection weight
HISTORY_BLOCK = 5                // Prevent repeats within 5 words
```

### Spacing & Timing
```javascript
REVIEW_THRESHOLD_DAYS = 2        // Priority threshold for reviews
RETRY_DELAY = 3                  // Show 3 words before retry
REVIEW_MISSED_MULTIPLIER = 3     // Missed words appear 3x in review
```

## Testing Checklist

### Basic Flow
- [ ] Fresh load (no student name) → shows welcome with overview
- [ ] Enter name → shows level picker
- [ ] Select level → starts showing words
- [ ] Mark words right/wrong → progress updates
- [ ] Master all words → shows review/test options
- [ ] Complete review → offers test
- [ ] Pass test → earns badge with celebration

### Cross-Level Review
- [ ] Previous level words appear during practice
- [ ] Tricky words from previous levels show up
- [ ] Distribution roughly 70/15/15
- [ ] Recent levels appear more than older levels
- [ ] Works on same day as mastering previous level

### All Known Words Mode
- [ ] Accessible via level picker (9th option)
- [ ] Shows only mastered words from all levels
- [ ] Streak counter appears
- [ ] Rainbow chase effect at streak 22+
- [ ] Can switch back to color levels
- [ ] Reset Level button hidden

### Modals & Navigation
- [ ] Welcome back modal shows correct options
- [ ] Continue current level works without crash
- [ ] Level picker shows 3×3 grid
- [ ] Profile modal shows all sections
- [ ] Import/export buttons work
- [ ] Reset All requires confirmation

### Import/Export
- [ ] Export creates valid JSON file
- [ ] Mobile share sheet works (if supported)
- [ ] Import validates data
- [ ] Import shows confirmation modal
- [ ] Import restores all progress correctly
- [ ] Import from welcome screen works

### Edge Cases
- [ ] All words mastered in level → shows review/test options
- [ ] Failed review → restarts with missed word priority
- [ ] Failed test → allows retest
- [ ] No mastered words → All Known Words shows 0 words
- [ ] No tricky words → distribution adjusts to 80/20
- [ ] Switching levels resets state properly

## Known Issues & Solutions

### Fixed Issues
- ✅ Loading screen on boot → Fixed by proper state initialization
- ✅ Modal state conflicts → Fixed with unique IDs and proper cleanup
- ✅ Button functionality → Fixed event listener setup
- ✅ Progress bar timing → Added updateProgressBars() before modals
- ✅ Same-day cross-level → Removed day threshold for mastered words
- ✅ Mode switching → Properly reset reviewMode in switchLevel()
- ✅ Phase badge errors → Converted to no-op function

### Current Limitations
- Single student per browser (use export/import for multiple students)
- Progress tied to browser (use export/import to transfer)
- No cloud sync (intentional - privacy and simplicity)

## Development Guidelines

### Code Organization
1. **Constants**: Defined at top of script
2. **State**: Global variables for runtime state
3. **Helper Functions**: Utility functions (shuffle, getDaysSince, etc.)
4. **Core Logic**: Word selection, mastery checking
5. **UI Functions**: DOM manipulation and updates
6. **Event Listeners**: At bottom of script
7. **Boot**: Initialization at very end

### Best Practices
- Always call `saveState()` after state changes
- Update UI after state changes (updateHeader, updateProgressBars, etc.)
- Use `console.log()` for debugging key functions
- Validate data before importing
- Handle null elements gracefully

### Adding New Features
1. Add HTML structure if needed
2. Add CSS styling
3. Add to `els` object if new element
4. Create/modify functions
5. Add event listeners
6. Update this documentation
7. Test thoroughly
8. Commit with descriptive message

## Maintenance

### Regular Updates
- Update this file after significant changes
- Document new functions and their purpose
- Add bug fixes to "Recent Updates" section
- Update testing checklist for new features
- Keep constants section current

### Version History
- **v3**: Current version with import/export and enhanced UI
- **v2**: Added general review mode and streak counter
- **v1**: Original implementation with 8 levels

## Contact & Support

This documentation should be updated by any agent working on the codebase to ensure continuity and proper understanding of the application's functionality.

**Last Updated**: October 2025