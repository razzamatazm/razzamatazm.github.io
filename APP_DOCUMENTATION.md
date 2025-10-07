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
- **Trigger**: When ALL words in level are mastered
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

### Streak Counter (General Review Mode)
- **Visual Bar**: 20 segments that fill as streak increases
- **Streak Range**: 0 to 22 (capped at 22 for challenge)
- **Glow Effect**: Appears when bar fills completely (streak 20)
- **Rainbow Effect**: Appears after maintaining full bar for 2 more correct answers (streak 22)
- **Gentle Penalty**: Wrong answers only reduce streak by 1 segment
- **Reset on Entry**: Streak resets to middle (10) when entering general review mode

### Review Modes
- **Current Level Focus**: Prioritizes current level, minimal previous level inclusion
- **General Review**: Shows only mastered words from ALL levels (Red through Light Blue), with enhanced tricky word priority for retention. Uses dedicated word selection logic that bypasses current-level restrictions.

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
- **Study Mode**: Single "Mastery" progress bar showing word mastery progress, Statistics pills (Learned, Remaining, Seen, Tricky)
- **Review Mode**: Single "Review Progress" bar showing review session progress, Statistics pills (Learned, Remaining, Seen, Tricky)
- **Test Mode**: Single "Test Progress" bar showing test completion progress, Statistics pills (Learned, Remaining, Seen, Tricky)
- **General Review Mode**: "Streak:" label with visual streak bar (20 segments), gentle dip on wrong answers, glow when bar fills (20), rainbow after maintaining streak (22)

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
8. **Fixed General Review Mode**: Now properly shows only mastered words from all levels
9. **Enhanced Review Mode UI**: Phase badge shows "Review Mode" when in general review
10. **Improved Review Mode Switching**: Exiting general review now opens level picker
11. **Enhanced Tricky Word Priority**: Tricky words get higher priority in general review mode
12. **Added Streak Counter**: Replaces status bars in review mode with engaging streak counter
13. **Implemented Visual Effects**: Glowing effects at 10+ streak, rainbow effects at 15+ streak
14. **Enhanced Engagement**: Streak counter starts at middle (10/20) and dips down gently on wrong answers
15. **Kinder-Friendly Design**: Streak only dips by 1 segment on wrong answers (not 3)
16. **Independent Review Mode**: General review mode works separately from test/review phases
17. **Fixed Streak Reset**: Streak meter resets to middle (10) when entering general review mode
18. **Fixed All-Levels Selection**: General review now properly includes mastered words from ALL levels
19. **Perfect Glow/Rainbow Timing**: Glow appears when bar fills (20), rainbow after maintaining streak for 2 more (22), capped at 22 for challenge
20. **Removed Streak Counter Text**: Clean visual-only streak bar without distracting numbers
21. **Clean Review Mode Interface**: Hidden progress bars in general review mode, only shows "Streak:" label and visual bar
22. **Fixed All-Levels Word Selection**: General review mode now truly shows words from ALL mastered levels, not just current level
23. **Dedicated General Review Logic**: Bypasses all current-level-specific logic when in general review mode
24. **Simplified Progress Bar Display**: Progress bars now show only one bar at a time - "Mastery" during study phase, "Review Progress" during review phase, and "Test Progress" during test phase
25. **Fixed Test Trigger**: Changed test offering to require ALL words mastered instead of 80% threshold
26. **Import/Export Feature**: Added backup and restore functionality with mobile-optimized sharing and clipboard support

### Known Issues & Solutions
- **Text appearing in word display**: Fixed by adding phase checks in `nextWord()`
- **Modal state conflicts**: Resolved by using unique IDs and proper state management
- **Button functionality**: Fixed by ensuring proper event listener setup
- **General review mode showing only current level**: Fixed by implementing dedicated logic that bypasses current-level restrictions
- **Streak counter timing issues**: Fixed with proper glow/rainbow timing and streak capping

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

### Import/Export Feature
- **Export Progress**: Creates a JSON backup file with all student progress
  - Mobile: Uses native share sheet to share via text/email or copies to clipboard
  - Desktop: Downloads a `.json` file
  - File naming: `rainbow-words-{studentName}-{date}.json`
- **Import Progress**: Restores progress from backup
  - Supports file upload or paste from clipboard
  - Validates data before importing
  - Warns user about replacing current progress
  - Mobile-friendly with multiple input methods

### Future Enhancements
- Additional level customization
- Progress analytics
- Multi-student support via separate export files

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
