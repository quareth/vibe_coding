# Playwright Workflow Patterns

Use these patterns as adaptable recipes. Discover the current application's
routes, labels, test data, and authentication flow before acting. Never assume
an endpoint, credential, selector, or product-specific state.

## Start with discovery

1. Open the user-provided URL or the repository's documented local URL.
2. Capture a snapshot and inspect the visible page structure.
3. Read relevant repository guidance when testing a local application.
4. Identify stable user-facing labels and roles before clicking or typing.
5. Keep the same named browser session while a workflow is in progress.

```bash
"$PWCLI" --session app open http://localhost:3000 --headed
"$PWCLI" --session app snapshot
```

Prefer accessible roles, labels, placeholder text, and visible names. Use
snapshot references only within the snapshot that produced them.

## Navigation and inspection

```bash
"$PWCLI" --session app goto http://localhost:3000/example
"$PWCLI" --session app snapshot
"$PWCLI" --session app get url
"$PWCLI" --session app get title
```

After navigation, wait for a meaningful visible condition rather than a fixed
sleep. Capture another snapshot whenever the page changes substantially.

## Form submission

1. Inspect the form.
2. Fill fields using labels or stable snapshot references.
3. Select checkboxes, radio buttons, or dropdown values explicitly.
4. Submit once.
5. Verify both visible confirmation and any relevant URL or state change.

```bash
"$PWCLI" --session app snapshot
"$PWCLI" --session app fill <field-ref> "example value"
"$PWCLI" --session app click <submit-ref>
"$PWCLI" --session app snapshot
```

Do not use real personal data when representative placeholders are sufficient.

## Authentication

- Reuse an existing signed-in browser session when available.
- Use credentials only when the user supplied or authorized them.
- Never print secrets, tokens, cookies, or password values.
- Verify successful authentication through visible account state or the
  expected protected page, not by exposing stored credentials.
- If multifactor authentication or a CAPTCHA appears, pause for the user.

## Asynchronous UI flows

For background work, uploads, streaming results, or delayed transitions:

1. Trigger the action once.
2. Wait for a specific loading, progress, success, or error state.
3. Inspect the updated UI.
4. Verify the final outcome and any user-visible error details.

Avoid fixed sleeps unless the application exposes no observable condition.
When a fixed wait is unavoidable, keep it short and explain why.

## Dialogs, new tabs, and downloads

- Inspect before accepting destructive confirmation dialogs.
- Track the active page after opening a new tab or popup.
- Save downloads only to a user-approved or repository-appropriate output
  directory.
- Verify downloaded filenames and non-empty content before reporting success.

## Screenshots and visual evidence

```bash
"$PWCLI" --session app screenshot
```

Capture screenshots when visual layout, clipping, responsive behavior, or a
specific rendered state matters. Prefer snapshots for semantic interaction and
screenshots for visual evidence.

## Debugging a failing flow

1. Reproduce the smallest failing sequence.
2. Capture a snapshot at the failure point.
3. Record the current URL and visible error.
4. Inspect browser console and network activity when the CLI supports it.
5. Separate application failures from environment, server, or selector issues.
6. Report the exact last successful step and first failing step.

Do not change application code unless the user asked for a fix.

## Data extraction

- Extract only the fields requested by the user.
- Preserve source order unless the user asks for sorting.
- Record pagination or filtering limits.
- Avoid collecting sensitive data not required for the task.
- Validate a small sample against the rendered page before reporting totals.

## Local application verification

Before testing a local application:

1. Confirm the documented start command and expected URL.
2. Check that required services are ready.
3. Use repository-provided seed or fixture data when available.
4. Avoid mutating shared environments unless the user authorized it.
5. Clean up test records when the workflow created disposable data and cleanup
   is safe and in scope.

## Completion checklist

- The intended user-visible outcome is verified.
- Assertions rely on stable observable behavior.
- No credentials or sensitive session data were exposed.
- Screenshots or extracted artifacts are saved in the expected location.
- The browser session is closed when it is no longer needed.
