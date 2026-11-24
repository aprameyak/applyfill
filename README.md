# ApplyFill

Chrome extension that fills job applications from a profile stored on your own device. No account, no server, no analytics. An optional on-device language model handles open-ended questions.

## What it does

- Fills names, contact details, links, work history, education, work authorization, and voluntary self-identification fields.
- Handles native selects, radio groups, checkboxes, ARIA comboboxes, file inputs, and forms inside open shadow roots.
- Follows multi-step forms. After your first fill on a page, fields that appear later are filled too.
- Reads every value back after filling and tells you which fields the site did not accept.
- Remembers answers you type and corrections you make, and reuses them.
- Imports a PDF, DOCX, or text resume into a draft profile that you review before saving.
- Never submits an application.

## Install

From a release:

1. Download `applyfill-<version>.zip` from the releases page and unzip it.
2. Open `chrome://extensions`, enable Developer mode, and choose Load unpacked.
3. Select the unzipped folder.

From source:

```sh
npm ci
npm run build
```

Then load the `dist` folder as an unpacked extension. Chrome 124 or newer is required.

After loading the extension for the first time, you can reload it whenever you rebuild:

1. Run `npm run build` to generate the updated `dist` folder.
2. Open `chrome://extensions` in Chrome.
3. Find the ApplyFill extension and click its **Reload** icon.
4. Refresh the webpage where you are testing ApplyFill if needed.

### Debugging extension logs

To inspect logs while debugging:

- **Background service worker:** Open `chrome://extensions`, find ApplyFill, and click the **service worker** link under Inspect views. This opens its DevTools console.
- **Popup:** Open the ApplyFill popup, right-click inside it, and select **Inspect** to view its console.
- **Content script:** Open the webpage where ApplyFill is running, open Chrome DevTools, and check the **Console** tab. Select the relevant frame or execution context if needed.

## Use

1. The settings page opens after install. Upload a resume or fill in the profile, then save.
2. Open an application page. A small Fill chip appears on pages that look like applications. You can also use the toolbar popup or press Alt+Shift+F.
3. Review the result. Fields that could not be filled or verified are listed in the popup.

Nothing is filled until you ask.

## Local model

The model is optional and off by default. When enabled it is used only for free-text questions and option lists that your profile and saved answers cannot resolve.

- Runs in the extension service worker through WebLLM and WebGPU.
- Default model: Qwen2.5 1.5B Instruct (Apache 2.0). Smaller and larger variants are selectable.
- Weights download after you approve, from huggingface.co, and are cached in the browser. The runtime library comes from raw.githubusercontent.com. Neither request carries your data.
- Output is checked before use. An answer is dropped if it is not marked as grounded in your profile, if it contains numbers or names that are not in your profile, if a choice is not one of the offered options, or if the question touches sensitive topics such as criminal history, disability, or demographics.

Without WebGPU, the extension still fills everything it can from your profile and saved answers.

## Privacy

All data lives in this browser's IndexedDB. See [PRIVACY.md](PRIVACY.md).

## Development

```sh
npm ci
npm run check        # typecheck and unit tests
npm run build        # builds dist/
npm run test:e2e     # Playwright, loads dist/ into Chromium
npm run package      # release/applyfill-<version>.zip
```

The model test downloads weights and is skipped unless `RUN_MODEL_TEST=1` is set.

Layout:

| Path | Contents |
| --- | --- |
| `src/content` | Field discovery, classification, filling, verification |
| `src/background` | Message handling, storage access, local model |
| `src/options`, `src/popup` | React interfaces |
| `src/parsing` | Resume text extraction and parsing |
| `src/shared` | Schema, storage, prompt building, output validation |
| `tests` | Unit tests, Playwright specs, HTML fixtures |

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md), [docs/PRD.md](docs/PRD.md), and [docs/ROADMAP.md](docs/ROADMAP.md).

## Contributing

Bug reports with a minimal HTML snippet of the problem form are the most useful.

- Guide: [CONTRIBUTING.md](CONTRIBUTING.md)
- Starter tasks: [good first issue](https://github.com/aprameyak/applyfill/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22)
- Field fill bugs: use the **Form not filling correctly** issue template

## License

MIT
