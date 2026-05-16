# ChaiAI

Static ChaiAPI chat, image, and video web app.

## GitHub Pages

Deploy the repository root or the `dist` folder as a static site. ChaiAPI should load it with hash params:

```text
https://<user>.github.io/<repo>/#key={key}&address={address}
```

The app reads the ChaiAPI token and API address at runtime. No API key is stored in the source code.
