# Sight Words Trainer - Rainbow Levels App Documentation

## Overview
This is a progressive sight words learning application with 8 color-coded levels (Red through Light Blue). Students master words through study, review, and testing phases to earn badges.

## Core Architecture

### File Structure
- `index.html` - Single-file application containing all HTML, CSS, and JavaScript
- `CNAME` - GitHub Pages custom domain configuration
- `README.md` - Basic project information

### Key Components

#### 1. Level System
```javascript
const LEVELS = [
  { name:'Red', color:'#ef4444', badge:'🍓', words:[...] },
  { name:'Orange', color:'#f97316', badge:'🍊', words:[...] },
  // ... 8 total levels
];
```

#### 2. State Management
- **Storage**: Uses `localStorage` with key `'sightwords_rainbow_v3'`
- **State Structure**: 
  - `perLevel`: Array of level data with word progress
  - `levelIdx`: Current level index
  - `phase`: Current phase ('study', 'review', 'final', 'prefinal', 'postreview')
  - `studentName`: Student's name
  - `reviewMode`: 'current' or 'general'

#### 3. Word Progress Tracking
Each word tracks:
- `seen`, `correct`, `wrong`: Basic counters
- `streak`: Current correct streak
- `mastered`: Boolean for mastery status
- `tricky`: Boolean for difficult words
- `lastSeenTimestamp`: Date of last appearance
- `masteryDates`: Array of correct answer dates
- `sessionsSeen`: Number of sessions word appeared in

## User Flow

### 1. Initial Load
- **No Student Name**: Shows welcome modal to enter name and practice mode
- **Has Student Name**: Shows "Welcome back" modal with three options:
  - Continue current level
  - Choose different level  
  - Review all known words

### 2. Study Phase
- **First Pass**: Shows each word once before repeats
- **Weighted Selection**: Uses unified pool system prioritizing:
  - Words not seen in 7+ days (critical)
  - Words with wrong answers
  - Tricky words
  - Cross-level review (previous levels)

### 3. Review Phase
- **Trigger**: When all words in level are mastered (or 80%+)
- **Process**: Each word appears twice in random order
- **Success**: Shows "Review" + "Retest" options
- **Failure**: Returns to study mode with missed words marked tricky

### 4. Test Phase
- **Trigger**: After successful review or direct selection
- **Process**: Each word appears once
- **Success (100%)**: Shows celebration animation + "Next Level" + "Choose Level" options
- **Failure**: Shows "Quick Review" option

## Key Functions

### Core Functions
- `nextWord()`: Main word selection logic
- `markRight()` / `markWrong()`: Handle user responses
- `pickFromUnifiedPool()`: Advanced word selection algorithm
- `checkMastery()`: Determines if word is mastered
- `showWelcomeBack()`: Creates welcome modal
- `openPreFinal()`: Shows review/retest options

### Modal Functions
- `showReviewSuccessModal()`: After successful review
- `endTestAndShowResults()`: After test completion
- `showBadgeCelebration()`: Badge earning animation

## Learning Science Features

### Mastery Requirements
- **Streak**: 3 correct answers in a row
- **Sessions**: Word must appear in 1+ session
- **Time Span**: 0 days between first and last correct (same-day mastery allowed)

### Spacing & Review
- **Critical Review**: Words not seen in 7+ days get highest priority
- **Retry Logic**: Failed words appear after 3 other words
- **Cross-Level Review**: Previous level words included based on review mode

### Review Modes
- **Current Level Focus**: Prioritizes current level, minimal previous level inclusion
- **General Review**: Balanced approach for long-term retention

## UI Components

### Header
- Phase badge (Study Mode, Review Session, Final Test, etc.)
- Level chip with colored dot
- Student name (clickable for profile)
- Badge row showing earned badges
- Control buttons (Review Mode, Level, Reset Level, Reset All)

### Main Area
- Large word display
- Left/right tap zones for "Missed it" / "Got it!"
- Keyboard shortcuts: W = wrong, R = right

### Footer
- Mastery progress bar
- Final review progress bar
- Statistics pills (Learned, Remaining, Seen, Tricky)

### Modals
- **Welcome**: Name entry and practice mode selection
- **Level Picker**: Grid of all levels with progress
- **Pre-Final**: Review/Retest options
- **Test Results**: Score and missed words
- **Success**: Badge celebration with next level options
- **Profile**: Student stats and badge collection

## Recent Updates & Bug Fixes

### Latest Changes (as of current session)
1. **Fixed Loading Issue**: Prevented `nextWord()` from being called when welcome modal is open
2. **Fixed JavaScript Errors**: Resolved null element references in modal functions
3. **Implemented Welcome Back Flow**: Added proper welcome modal with auto-level detection
4. **Fixed Review/Retest Loop**: Added proper modal state management
5. **Improved Button Styling**: Made all welcome modal buttons consistent
6. **Fixed Button Conflicts**: Resolved ID conflicts between header and modal buttons
7. **Enhanced Badge Celebration**: Improved confetti animation and timing

### Known Issues & Solutions
- **Text appearing in word display**: Fixed by adding phase checks in `nextWord()`
- **Modal state conflicts**: Resolved by using unique IDs and proper state management
- **Button functionality**: Fixed by ensuring proper event listener setup

## Development Notes

### Debugging
- Console logging added to key functions for troubleshooting
- State inspection available via browser dev tools
- Local storage can be cleared to reset app state

### Performance
- Single-file architecture for simplicity
- Efficient word selection algorithm
- Minimal DOM manipulation
- Responsive design for mobile/desktop

### Future Enhancements
- Additional level customization
- Progress analytics
- Multi-student support
- Export/import progress data

## Maintenance

### Keeping Documentation Updated
1. **After Major Changes**: Update this file with new features/fixes
2. **Function Changes**: Document new functions and modifications
3. **Bug Fixes**: Add to "Recent Updates" section
4. **UI Changes**: Update component descriptions
5. **Flow Changes**: Update user flow diagrams

### Testing Checklist
- [ ] Fresh load (no student name)
- [ ] Returning student (with name)
- [ ] Level progression
- [ ] Review completion
- [ ] Test completion
- [ ] Badge earning
- [ ] Cross-level review
- [ ] Button functionality
- [ ] Modal interactions

## Contact & Support
This documentation should be updated by any agent working on the codebase to ensure continuity and proper understanding of the application's functionality.
