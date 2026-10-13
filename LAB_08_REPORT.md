# Lab 08: The Apollo 11 AGC Code Exploration & Analysis

**Student:** Senior Computer Science / Software Engineering Major  
**Repository:** `MONYROTH007/Lab_08`  
**Target Codebase:** NASA Apollo Guidance Computer (AGC) – Luminary 1A Build 099 (Lunar Module)  

---

## Executive Summary
This report documents the completion of the Lab 08 assignment: locating famous developer commentary across the Apollo 11 Lunar Module flight software (`Luminary099`), stress-testing AI comprehension against 1969 AGC assembly idioms, investigating the historic 1202 program alarm in the Executive scheduler, and porting astronaut-interactive routines to modern 2026 JavaScript with original comments preserved.

---

## Round 1: The Comment Hunt

All 10 target comments were located within the `Luminary099` source tree using search indexing and verified directly against line numbers in the original digitized MIT transcripts.

| # | Comment String | File | Line Number(s) | Routine / Context |
|---|---|---|---|---|
| 1 | `OFF TO SEE THE WIZARD...` | [`Luminary099/THE_LUNAR_LANDING.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/THE_LUNAR_LANDING.agc#L254) | Line 254 | `P63SPOT4`: Transfer of control (`TC POSTJUMP`) to [`BURNBABY`](file:///Users/mac/Documents/Apollo-11/Luminary099/THE_LUNAR_LANDING.agc#L255) for descent engine master ignition. |
| 2 | `SEE IF HE'S LYING` | [`Luminary099/THE_LUNAR_LANDING.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/THE_LUNAR_LANDING.agc#L249) | Line 249 | `P63SPOT3`: Re-reading hardware discrete channel 33 bit 6 after the astronaut hits DSKY "PROCEED" on checklist 500. |
| 3 | `SILLY THING AROUND` | [`Luminary099/THE_LUNAR_LANDING.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/THE_LUNAR_LANDING.agc#L246) | Line 246 | `P63SPOT3`: Requesting astronaut to manually re-orient landing radar antenna into position 1 via checklist `CODE500`. |
| 4 | `NOW LOOK WHERE YOU ENDED UP` | [`Luminary099/THE_LUNAR_LANDING.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/THE_LUNAR_LANDING.agc#L317) | Line 317 | `P68`: Landing confirmation. Flashing Verb 06 Noun 43 (Latitude, Longitude, Altitude) to show the crew where they touched down. |
| 5 | `TEMPORARY, I HOPE HOPE HOPE` | [`Luminary099/LUNAR_LANDING_GUIDANCE_EQUATIONS.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/LUNAR_LANDING_GUIDANCE_EQUATIONS.agc#L179-L180) | Lines 179–180 | `VRTSTART`: Calling [`STOPRATE`](file:///Users/mac/Documents/Apollo-11/Luminary099/LUNAR_LANDING_GUIDANCE_EQUATIONS.agc#L180) to kill attitude angular rates when entering vertical descent phase (`WCHPHASE = 2`). |
| 6 | `NUMERO MYSTERIOSO` | [`Luminary099/LUNAR_LANDING_GUIDANCE_EQUATIONS.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/LUNAR_LANDING_GUIDANCE_EQUATIONS.agc#L666) | Line 666 | Guidance targeting loop: empirical braking gain constant [`GAINBRAK,1`](file:///Users/mac/Documents/Apollo-11/Luminary099/LUNAR_LANDING_GUIDANCE_EQUATIONS.agc#L666) scaled against time-to-go. Placed on line 666. |
| 7 | `NO ROOM IN THE INN` | [`Luminary099/WAITLIST.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/WAITLIST.agc#L335) | Line 335 | `FILLED`: The Waitlist delta-time task array has run out of slots; triggers non-abortive restart alarm `01203`. |
| 8 | `TRASHY LITTLE SUBROUTINES` | [`Luminary099/LUNAR_LANDING_GUIDANCE_EQUATIONS.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/LUNAR_LANDING_GUIDANCE_EQUATIONS.agc#L1375) | Line 1375 | Header dividing mathematical guidance formulas from small helper subroutines ([`INTPRETX`](file:///Users/mac/Documents/Apollo-11/Luminary099/LUNAR_LANDING_GUIDANCE_EQUATIONS.agc#L1378), [`TDISPSET`](file:///Users/mac/Documents/Apollo-11/Luminary099/LUNAR_LANDING_GUIDANCE_EQUATIONS.agc#L1384)). |
| 9 | `COME AGAIN SOON` | [`Luminary099/BURN_BABY_BURN--MASTER_IGNITION_ROUTINE.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/BURN_BABY_BURN--MASTER_IGNITION_ROUTINE.agc#L925) | Line 925 | `GOBACK`: In master ignition routine, returning execution control to caller via [`TEMPR60`](file:///Users/mac/Documents/Apollo-11/Luminary099/BURN_BABY_BURN--MASTER_IGNITION_ROUTINE.agc#L924) with a bank jump. |
| 10 | `DO NOT USE GOPROG2 OR ENEMA WITHOUT CONSULTING POOH PEOPLE.` | [`Luminary099/FRESH_START_AND_RESTART.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/FRESH_START_AND_RESTART.agc#L353) | Line 353 | Cautionary block comment above constant table: calling the memory reset/purge routine [`ENEMA`](file:///Users/mac/Documents/Apollo-11/Luminary099/FRESH_START_AND_RESTART.agc#L281) breaks Program 00 ("POOH" idle mode). |

### Bonus Question: The Arcade Game File
- **Filename:** [`Luminary099/PINBALL_GAME_BUTTONS_AND_LIGHTS.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/PINBALL_GAME_BUTTONS_AND_LIGHTS.agc) (also paired with [`PINBALL_NOUN_TABLES.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/PINBALL_NOUN_TABLES.agc)).
- **What is it named after?** An arcade **Pinball** machine.
- **What does it do?** It is the DSKY (Display and Keyboard) operating subsystem. It manages the human-machine interface for the astronauts: reading numeric keypad strokes (Verbs and Nouns) and lighting up the seven-segment displays and annunciator indicator lights. MIT developer Ramón Alonso nicknamed it "PINBALL" because pressing buttons caused banks of lights to blink and chatter exactly like an electro-mechanical arcade pinball machine.

---

## Round 2: Stump the Model

### 1. Analysis of Three Selected Routines

#### A. The Astronaut Antenna Check: [`THE_LUNAR_LANDING.agc:245-250`](file:///Users/mac/Documents/Apollo-11/Luminary099/THE_LUNAR_LANDING.agc#L245-L250)
```assembly
		CAF	CODE500		# ASTRONAUT:	PLEASE CRANK THE
		TC	BANKCALL	#		SILLY THING AROUND
		CADR	GOPERF1
		TCF	GOTOPOOH	# TERMINATE
		TCF	P63SPOT3	# PROCEED	SEE IF HE'S LYING
```
- **What it does:** Program 63 tests bit 6 of I/O Channel 33. If the Landing Radar antenna discrete is not asserted (Position 1), it requests checklist code 500 via DSKY (`V50N25`). If the astronaut hits TERMINATE (`V34E`), it aborts to idle P00. If the astronaut presses PROCEED (`V33E`), the instruction `TCF P63SPOT3` loops straight back to line 239 to read Channel 33 again.
- **Why the comment exists:** The programmer knew that an astronaut under high mission stress might simply press "PROCEED" on the keypad assuming the antenna had already auto-deployed. The comment `SEE IF HE'S LYING` captures the defense mechanism: trust, but verify the physical switch contact.

#### B. The Post-Touchdown Coordinates: [`THE_LUNAR_LANDING.agc:317-323`](file:///Users/mac/Documents/Apollo-11/Luminary099/THE_LUNAR_LANDING.agc#L317-L323)
```assembly
		CAF	V06N43*		# ASTRONAUT:  NOW LOOK WHERE YOU ENDED UP
		TC	BANKCALL
		CADR	GOFLASH
		TCF	GOTOPOOH	# TERMINATE
		TCF	+2		# PROCEED
		TCF	-5		# RECYCLE
```
- **What it does:** P68 (Landing Confirmation) executes immediately after touchdown on the lunar surface. It zeros out attitude error, puts the RCS control in a 5-degree deadband, converts coordinates to selenographic latitude/longitude/altitude, and flashes Verb 06 Noun 43.
- **Why the comment exists:** The comment is classic MIT engineering wit. The astronauts spent months training for precise pin-point landings; upon engine shutdown, the computer flashes the screen saying effectively: "Well, here is where you actually landed."

#### C. The Waitlist Exhaustion: [`WAITLIST.agc:334-337`](file:///Users/mac/Documents/Apollo-11/Luminary099/WAITLIST.agc#L334-L337)
```assembly
FILLED		DXCH	WAITEXIT
		TC	BAILOUT1	# NO ROOM IN THE INN
		OCT	01203
```
- **What it does:** The AGC Waitlist handles scheduled real-time tasks delayed by specific intervals. If an interrupt or job attempts to schedule a task when all available waitlist slots are filled, execution jumps to `FILLED`.
- **Why the comment exists:** A biblical reference to the Nativity ("no room in the inn")—the scheduler table has zero remaining beds, so it must trigger alarm 01203 (`WAITLIST OVERFLOW - TOO MANY TASKS`).

---

### 2. Common LLM Failure Modes on 1969 AGC Assembly
When querying LLMs regarding AGC assembly without rigorous checks, models typically exhibit distinct hallucination patterns:
1. **Misreading Instruction Opcodes:** Confusing `TC` (Transfer Control / subroutine call), `TCF` (Transfer Control Fixed / unconditional jump), and `CCS` (Count, Compare, and Skip / four-way branch).
2. **Interpreter Syntax Confusion:** The AGC had a built-in virtual machine ("The Interpreter") used for high-precision vector and matrix math (`INTPRET`, `VLOAD`, `UNIT`, `VXSC`). Models frequently conflate basic machine instructions with interpretive opcodes.
3. **Attributing Comments to the Wrong Block:** Because AGC code uses an indented comment style where comments span multiple successive assembly instructions (e.g. `PLEASE CRANK THE` on line 245 followed by `SILLY THING AROUND` on line 246), LLMs often attribute the sentence to only one opcode or invent nonexistent subroutines.

---

### 3. The Big One: Alarm 1202 in `Luminary099/EXECUTIVE.agc`

In [`Luminary099/EXECUTIVE.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/EXECUTIVE.agc#L201-L208):

```assembly
NEXTCORE	CAF	COREINC
		ADS	LOCCTR
		CCS	EXECTEM2
		TCF	NOVAC3
		LXCH	EXECTEM1
		CA	Q
		TC	BAILOUT1	# NO CORE SETS AVAILABLE.
		OCT	1202
```

#### Code Path & What Happens Next
1. **Raising the Alarm:** `NEXTCORE` searches through the Executive's core sets (priority process control blocks stored in erasable memory). If `EXECTEM2` counts down to zero without finding a free core set, the computer has no memory left to schedule a new job. It loads the return address from `Q` and jumps to `TC BAILOUT1` with literal octal `1202`.
2. **Destination Routine:** `BAILOUT1` is defined in [`Luminary099/ALARM_AND_ABORT.agc:189`](file:///Users/mac/Documents/Apollo-11/Luminary099/ALARM_AND_ABORT.agc#L189):
   - `BAILOUT1` executes `INHINT` (inhibit interrupts).
   - It records the alarm code `1202` into erasable register `FAILREG` and activates the **PROG ALARM** caution lamp on the DSKY.
   - It performs a **software restart (BAILOUT)**: it flushes low-priority scheduled jobs and unneeded core sets while preserving mission-critical state variables stored in phase tables.
3. **Historical Reality Check:**  
   During the actual Apollo 11 descent on July 20, 1969, the rendezvous radar was inadvertently left in the wrong power/mode setting, bombarding the AGC with 800 hardware cycle-steals per second. This overloaded the Executive with job requests.  
   **The computer did not crash.** Thanks to the asynchronous priority executive designed by Hal Laning and Margaret Hamilton's team, the 1202 alarm dropped the superfluous radar data tasks and maintained the 20-millisecond throttle guidance loop, allowing Neil Armstrong and Buzz Aldrin to safely land.

---

## Round 3: Port a Comment to 2026

The P63 landing radar check logic from [`Luminary099/THE_LUNAR_LANDING.agc:239-256`](file:///Users/mac/Documents/Apollo-11/Luminary099/THE_LUNAR_LANDING.agc#L239-L256) was ported into modern JavaScript in [`port_to_2026.js`](file:///Users/mac/Documents/Apollo-11/port_to_2026.js).

### JavaScript Implementation
```javascript
/**
 * Routine: P63 Landing Radar Antenna Position Check
 * Original Comments Preserved Word-for-Word from THE_LUNAR_LANDING.agc
 */
async function checkLandingRadarAntenna(lm, dsky) {
  // P63SPOT3
  while (true) {
    // IS THE LR ANTENNA IN POSITION 1 YET
    const bit6 = lm.readChan33Bit6();

    // BRANCH IF ANTENNA ALREADY IN POSITION 1
    if (bit6 === 0) {
      break;
    }

    // ASTRONAUT:	PLEASE CRANK THE
    //		SILLY THING AROUND
    const response = await dsky.displayChecklist('00500');

    if (response === 'TERMINATE') {
      // TERMINATE
      return gotoPooh();
    }

    if (response === 'PROCEED') {
      // PROCEED	SEE IF HE'S LYING
      // Loop back to P63SPOT3 to re-verify Channel 33 hardware discrete:
      continue;
    }
  }

  // P63SPOT4
  // ENTER		INITIALIZE LANDING RADAR
  lm.initializeRadar();

  // OFF TO SEE THE WIZARD...
  return burnBabyMasterIgnition();
}
```

### Analysis
- **Does the 1969 comment still make sense?**  
  Yes. The phrase `SEE IF HE'S LYING` accurately describes defensive UI programming: validating actual system state rather than assuming user confirmation guarantees completion.
- **Did the check-on-the-human survive?**  
  Yes. The `while (true)` loop and `continue` structure ensures that selecting `PROCEED` does not progress until `readChan33Bit6()` verifies the hardware signal.

### Honest 1969-Style Comment in Student Code
Added to [`port_to_2026.js`](file:///Users/mac/Documents/Apollo-11/port_to_2026.js):
```javascript
function fetchUserDataWithSpaghettiFallback(userId, retryCount = 0) {
  // TEMPORARY, I HOPE HOPE HOPE
  try {
    if (!userId || typeof userId !== 'string') {
      // DO NOT USE GOPROG2 OR ENEMA WITHOUT CONSULTING POOH PEOPLE.
      throw new Error('Invalid user identifier provided to parser');
    }
    return { id: userId, token: `session_${Date.now()}` };
  } catch (err) {
    if (retryCount < 3) {
      return fetchUserDataWithSpaghettiFallback(userId, retryCount + 1);
    }
    // NOW LOOK WHERE YOU ENDED UP
    throw err;
  }
}
```

---

## Finished Early: The `BURN_BABY_BURN` Routine

Examining lines 33–65 of [`Luminary099/BURN_BABY_BURN--MASTER_IGNITION_ROUTINE.agc`](file:///Users/mac/Documents/Apollo-11/Luminary099/BURN_BABY_BURN--MASTER_IGNITION_ROUTINE.agc#L33-L65):

1. **Origin of the Name:**  
   MIT developers Don Eyles and Peter Adler named the master ignition program after **Magnificent Montague**, a charismatic 1950s–1960s R&B disc jockey in New York, Chicago, and Los Angeles who shouted *"Burn, baby! BURN!"* when playing hot soul records.
2. **The 1969 Design Document:**  
   The block comment under line 53 serves as a complete architectural specification written in direct, plain English capital letters:
   - Identifies every calling program: `P12, P40, P42, P61, P63`.
   - Defines the precise time boundaries: between `TIG - 45 seconds` (Time of Ignition) and `TIG + 26 seconds` (DPS throttle up).
   - Explains the parameterization table pattern using erasable register `WHICH`.
3. **Contrast with Modern Documentation:**  
   Unlike modern API documentation, which is often auto-generated, fragmented, or overly abstract, the 1969 MIT engineers treated assembly comments as direct peer-to-peer communication. They clearly state *what*, *why*, and *how*, with honest caveats about quirks and dependencies.

---

## Class Presentation Notes

- **Line reading:**  
  `TCF P63SPOT3 # PROCEED SEE IF HE'S LYING`  
  Preceded by `CADR GOPERF1` and followed by `P63SPOT4 TC BANKCALL`.
- **Key takeaway:**  
  The AGC developers balanced humor with life-critical defensive programming. The 1202 alarm was not a failure of software, but proof of the robustness of priority-driven asynchronous computing.
