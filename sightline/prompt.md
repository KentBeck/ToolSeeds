Prompt for in-app findings that reach the whole program:

Build a sightline into this application: a findings button that freezes what
I am looking at, captures it, lets me mark it up (circle, box, line,
freehand, arrow, numbers), and files it with everything that produced what
I see, in a fixed place in the repository. You, the coding agent, read it the
next time I say "look at the latest finding", and follow the line of sight
from the marked pixels back to the code that made them.

The interface is only the window. Most of what I will mark is not a widget
but a result: an object the algorithm placed badly, a shape the geometry
code bent wrong, a search result that shouldn't rank there, a schedule the
optimizer produced, a backend job that recomputed what it should have
cached. I trigger and steer those processes through the UI and judge them by
what they put on screen, but the fault sits behind it, in the algorithms,
the backend, the data. A finding is a defect report on the whole program,
with the screen as evidence. Interface glitches go through the same button.

The point is showing instead of describing. A red circle around one object
costs ten seconds and is unambiguous; a sentence about "the twisted one near
the middle" costs a round of guessing, and for an algorithm's output often
cannot be written at all.

This is a development tool for the people building the application, not a
support channel for end users. Unlike the other seeds, it is not published
as an artifact; it becomes part of the project.

SCOPE
- Map the program before designing anything: its views (the main screen,
  every sheet, dialog or sub-page with its own drawing surface), the
  pipeline from a UI action to what is drawn (which algorithm, backend
  route, worker, job or query runs, with which parameters, seeds and limits),
  and how the application saves its state. The finding reuses that save
  format instead of inventing one; if there is none, it uses the payload the
  application's own load path consumes.
- One user, one machine, a local server. If the application has no server
  of its own, add the smallest one the project's stack already allows, bound
  to localhost.
- Never ship the button or the filing endpoint in a production build. Gate
  both behind the project's dev mode and say how. Frameworks rarely have one
  flag for this: static files are often served in every mode, and tests may
  need the gate open. Prove it by building the production artifact and
  checking that the endpoint and the client script are absent from it, not
  just unreachable.
- For a web application, build it in the page. For a desktop or mobile
  application, use the toolkit's own window capture (e.g. QWidget::grab,
  UIGraphicsImageRenderer, PixelCopy) with the same filing format.

CAPTURE
- Real pixels, not a reconstruction. In a browser, use the screen-capture
  API (getDisplayMedia with preferCurrentTab, one frame, then stop every
  track). A DOM rasterizer (html2canvas and the like) re-renders the page
  and gets canvases, video, fonts and filters wrong, which is the wrong kind
  of answer for a tool whose purpose is "this is exactly what I saw".
- preferCurrentTab only suggests this tab; I can still pick another tab,
  window or screen. Check the track's displaySurface before using the
  frame, and if it isn't this tab, say so in the annotation view and offer
  retry and cancel. Record the surface in the sidecar.
- Capture first, then open the annotation view. Opening the view first makes
  the capture photograph the view itself. Make capture() refuse to run while
  the view is visible, and keep that guard under test.
- Freeze the moment by freezing the application's clock, not by stopping
  what moves. Stopping animations, timers, polling or playback changes
  button states, indicators and labels, so the image shows a state I never
  saw. If the application has no clock of its own, introduce one and route
  its timers, polls and animation frames through it. Resume exactly where it
  was when the view closes, whether I file or cancel. Say what keeps moving
  regardless: backend jobs, third-party widgets with their own timers.
- Don't throttle the capture stream's frame rate, and wait for the first real
  frame (requestVideoFrameCallback or loadeddata) with a timeout of a few
  seconds. Tab capture may deliver no frame at all for a page that doesn't
  repaint, so force an invisible repaint while waiting (one pixel changing
  its alpha by 1/255 is enough). A silent stream must then fall through to
  the fallbacks, not hang. A permission prompt that is still open is me
  deciding, not a failure: don't time it out.
- When capture is unavailable (permission denied, insecure context, an
  unsupported browser), offer a named fallback ladder and say on screen which
  step is in use and why: paste an image from the clipboard, then the main
  view's own drawing surface (its canvas, or its layers composited: tiles,
  SVG, canvas), naming what that leaves out. A partial image is marked
  incomplete in the image itself (a visible label) and in the sidecar. A
  pasted image has no known geometry, so its marks don't resolve to
  objects; record that too. No silent failure and no dead end.
- A sheet or sub-view that draws into its own canvas, if there is one, gets
  its own button and captures that canvas directly: pixel-exact, no
  permission dialog. Its source name goes into the sidecar, and the server
  only accepts known source names. Don't invent such a view.

WHAT PRODUCED IT
- The image shows the result; the sidecar must hold what produced it.
  Separate two claims and say which one holds:
  - rebuild: the displayed result can be rebuilt from the sidecar alone,
    without the UI and without me. This is required. Capture the result
    itself (the assignment, the layout, the ranked list) in the state, not
    only its inputs;
  - recompute: running the algorithm again from the captured inputs gives
    the same result. This needs every source of nondeterminism pinned:
    random seeds (seed what isn't seeded, and store the seed), but also
    wall-clock time limits, thread counts and anything read from the clock.
    If recompute can't be made to hold, say why; rebuild still has to.
- Into the sidecar go:
  - the application state in its save format, passed through as an opaque
    object, with every parameter, seed and limit that went into the result;
  - the commit and dirty flag of the running build, and the versions of the
    libraries and of any cache, store or precomputed data the result was
    drawn from;
  - the backend's side, attached by the server at filing time because the
    page can't see it: the jobs, requests and queries that ran in the last
    minutes with their parameters, durations, cache hits and misses (or the
    recomputations a missing cache causes), warnings and errors, capped and
    sanitized;
  - an action journal: the last ~200 user actions in a ring buffer from
    document-level listeners (button clicks with the label the control
    settles on once the action's request has returned, committed input and
    select changes with their value, one entry per zoom or drag gesture),
    with millisecond timestamps, mirrored to local storage so a crash
    doesn't lose it. Never record the value of a password field or of a
    field the application marks as sensitive; record that it changed. The
    state says where the program stood; the journal
    says how it got there, which is usually what the diagnosis needs.
- Resolve each mark to what lies under it. At filing, ask the application's
  own model (hit-testing, scene graph, the layout it computed) which domain
  objects each mark encloses, crosses or points at, and store their ids and
  the data that made them (seed, parameters, the pipeline stage that placed
  them, their geometry). "Mark 2" then means "object k24, placed by the
  packing step with seed 7", not a region of pixels. When the algorithm
  reports intermediate results (best-solution events, iterations, passes),
  diff them to say which stage placed each marked object. Take scores and
  explanations from the program's own explanation API where it has one; if
  you recompute them, say so, because a copy can drift from the real rule.
  Say which kinds of marks resolve and which don't.
- If the state can hold secrets or personal data (credentials, tokens,
  customer records), filter it through an allowlist of fields before it
  reaches the sidecar, and say what was left out.
- None of this enters saves, exports or the produced work. Raise the
  server's request-size limit for filing if the image and state need it.

MARKS
- Marks are data, not brush strokes. Keep each mark as {n, kind, colour,
  width, points, label} in image coordinates (not screen or window
  coordinates), burn it into the PNG, and write the same list into the
  sidecar. That is what makes "at mark 2" answerable without measuring
  pixels, and what makes undo trivial.
- Kinds: circle/ellipse, box, straight line, freehand, arrow, and a bare
  number. Every mark carries a number badge with its n; place the badge
  beside the stroke, not on it (on a short line it hides the line), and
  store its position as label. At least four colours including red and
  green, three widths. Every mark gets a thin contrasting halo so it reads
  on light and dark content alike.
- Undo removes the last mark only; discard removes all, after asking.
  Leaving the view with unsaved marks asks first. Points past the image edge
  are clamped to it, in the drawing and in the sidecar, so stored
  coordinates are always inside the image.
- A short note field, optional. A finding with no marks and no note is
  valid, but must not be filed by an accidental click.
- A keyboard shortcut opens the capture, but never while focus is in a text
  field.

FILING
- Three files per finding under one directory (e.g. docs/findings/), with a
  stem that carries the time: finding-YYYYMMDD-HHMMSS, plus -2, -3 … on
  collision. Never reuse or overwrite a stem, even when only one of its
  files is left. Order findings by timestamp and then the suffix as a
  number, not as text (-10 sorts before -9 as text).
  - <stem>.png: the annotated image, longest edge capped (2560 px is a good
    default), lossless
  - <stem>.thumb.png: longest edge ~360 px
  - <stem>.json: the sidecar: name, created (local time with offset), note,
    marks with the objects they resolve to, source (which view), complete
    (true/false), image size, everything under WHAT PRODUCED IT, status
    (open/done), verdict and a closing note
- If filing fails (server down, disk full), the annotated image must not be
  lost: offer it and the sidecar as downloads and say what failed.
- The server validates every name it is given against the stem pattern and
  refuses anything else; no path built from client input reaches the file
  system unchecked.
- Generate index.md (for the agent) and index.json (for the findings page)
  from the sidecars on every filing and every status change. Both are
  derived, never edited by hand, and rebuilt if missing. index.md lists open
  findings first, newest first, each with time, note (or "(no note)"), mark
  count, the objects marked, source and image path, then the closed ones
  with their verdict and closing note.
- Decide with me whether the images are versioned. Default: the image and
  sidecar files are ignored and kept in the project's backup; index.md and
  index.json are committed as the history of what was found and how it
  closed, and index.md says that the images are not in the repository.
  Screenshots grow fast and may show data that doesn't belong in a
  repository. Say how many megabytes a finding takes.

FINDINGS PAGE
- One page lists every finding, newest first, with thumbnail, time, note,
  source and status; open ones above closed ones. Click for the full image.
  Reachable in one click from wherever the application keeps its links.
- "Open in app" rebuilds the captured state through the application's own
  load path, so I can look at the same result again, live.
- Closing a finding asks for a verdict and a one-line cause: fixed (name the
  commit), intended, superseded, duplicate (name the other finding) or
  answered. Closed findings stay; nothing is deleted.

THE AGENT SIDE
- Add a short section to the project's agent instructions (CLAUDE.md,
  AGENTS.md or whatever the project uses; create the file beside the
  application if there is none) that says what follows.
- Where findings live, that index.md is the entry point, and that "the
  latest finding" means the newest open stem.
- A finding is read by looking at the PNG itself, then the marks, the
  objects they resolve to, the state, the backend log and the journal. The
  JSON alone is not the finding.
- Expect the words to arrive in the conversation rather than in the note
  field, and pair the finding with what I say about it.
- Diagnose along the sightline: rebuild the result from the sidecar outside
  the UI with one command, confirm the marked defect appears, find the
  pipeline stage that produced the marked objects, and measure there
  instead of arguing from the picture. If the result can't be rebuilt, say
  what is missing from the sidecar and fix the capture before the defect.
- Turn the finding into a check that fails before the fix and passes after,
  using the finding's own state as the fixture. When the marks judge a
  heuristic (a detector, a ranking, a classifier, a packing rule, a
  constraint weight), keep each marked object as a labeled example, so the
  next tuning can't silently undo what a finding taught.
- When a fix lands, close the finding with its verdict and the commit. The
  page and a command-line entry point share one closing routine, so the
  agent can close findings while the application isn't running and
  index.md stays generated.

VERIFY
- File a finding through the real button in a headless browser with a fake
  capture stream (Chromium: --use-fake-ui-for-media-stream,
  --auto-select-desktop-capture-source and, for tab capture,
  --auto-accept-this-tab-capture), draw three marks in two colours, one of
  them around a single produced object, type a note, file it. Then check
  that exactly three new files carry the new stem and that index.md and
  index.json were regenerated, that the PNG shows the application
  and not the annotation view (compare it with a screenshot taken just
  before the press), that the sidecar's marks match what was drawn and
  their number badges appear in the image, that the mark around the object
  resolves to that object's id, and that index.md lists the finding at the
  top.
- Rebuild the result from that sidecar in a fresh process, without the UI,
  and compare it with what was on screen: same objects, same positions, same
  numbers. A mismatch means the capture misses an input; find it. Commit one
  sidecar as a fixture so the build runs this comparison every time.
- Check that the backend log in the sidecar contains the job or request that
  produced the captured result.
- Start whatever moves (an animation, a running job, a poll), capture, and
  check that the state visible in the image (button labels, indicators,
  scores) matches the state before the press, that it doesn't change while
  the view is open, and that the clock resumes from the same value after
  closing.
- Deny the capture and check that the fallback is named on screen and the
  filed finding is marked incomplete in image and sidecar. Headless
  browsers often leave a capture prompt pending forever rather than deny
  it; a Permissions-Policy: display-capture=() response header produces a
  real denial.
- Stop the server and file: the image and sidecar are offered for download
  and the failure is named.
- Check collision (three filings in the same second), the size cap, the
  thumbnail, a name outside the stem pattern refused by the server, and the
  shortcut not firing inside a text field.
- Build the production artifact and check that the button's script and the
  filing endpoint are absent and the application still works.
- Reintroduce the open-before-capture order and confirm a test goes red;
  then also remove the guard and confirm the image comparison catches it.
  Run these browser checks in the same place the project's other tests run,
  or say why they can't.
- Look at one filed PNG yourself, as I will.
- Tell me: the button's location and shortcut, the directory, the fallbacks
  in order, what keeps moving during the freeze, what the sidecar captures
  from the page and from the backend and what it misses, which marks
  resolve to objects, whether rebuild and recompute hold, the size per
  finding, what is gitignored, how the production build excludes it, and
  the section you added to the agent instructions.
