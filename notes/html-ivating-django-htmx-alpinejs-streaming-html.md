## HTML-ivating your Django web app's experience with HTMX, AlpineJS, and streaming HTML
### by Chris May

- [slides](https://speakerdeck.com/chrismay/html-ivating-your-django-app-with-alpine-htmx-and-streaming-html)
- [poc repo](https://github.com/PyHAT-stack/web-async-patterns)
- [website](https://everydaysuperpowers.dev/)
- [mastodon](https://fosstodon.org/@_chrismay)

---

- SPAs (Single Page Applications)
  - initial page downloads JS
  - handles all interactions
  - use APIs to communicate with servers
  - changes HTML/url without changing pages
- concern: overuse of SPA pattern
  - incur complexity that doesn't exist with traditional applications
  - aren't aware of alternative approach
  - JS framework often "required"
- SPA popularity
  - better UX
  - interface updated in small payloads
  - traditional web apps can do the same too!
- ["Making the world's fastest website, and other mistakes" article](https://dev.to/tigt/making-the-worlds-fastest-website-and-other-mistakes-56na)
  - can initial download be 130k or less? for all html, css, js?
  - sprinkled html with just enough css and js for interactivity
  - demo app (20s) vs production app (4min)
    - functional equivalent, better for user
  - you can use Django to create an app like this!
- components of elevated experiences
  - remove whole-page refresh for every interaction
  - small payloads from server
  - update html from data changes
  - Caleb Porzio
    - every project would start as traditional web app, then morph into SPA. But why?
    - realized that if you return JSON from server, you need JS to parse it
    - if you returned HTML from server, you need less JS
    - [LiveWire](https://laravel-livewire.com/)
    - [Django Unicorn](https://www.django-unicorn.com/)
    - [HTMX](https://htmx.org/)
    - [AlpineJS](https://alpinejs.dev/) made by Caleb to support LiveWire
  - What about developer experience?
    - These frameworks use html attributes to control behavior
    - Locality
    - potentially remove JS build system!
    - easier to maintain - use Python/HTML
    - [David Guillot's talk on how his company switched from React to HTMX](https://www.youtube.com/watch?v=3GObi93tjZI)
      - what are the tradeoffs?
        - no negative tradeoffs
        - positive tradeoffs was more capability to render larger documents
        - faster overall
    - perceived speed
      - SPAs take more time to boot up
      - React mobile sites take > 10s to load
- deliver elements as quickly as possible
  - traditional web apps:
    - gather all data, templates, stitch entire template, then send it
  - we want to start rendering templates ASAP
    -  when data is available, render it as html fragment
    - streaming HTML responses, render skeleton elements so page doesn't jump around
- Django example
  - Django 4.2 - `StreamingHttpResponse`, `generate_async`
    - only works in Jinja templates
  - approaches
    - Option 1
      - split template into parts, yield each part via async streaming response
    - Option 2
      - render view, subscribe to server-sent events with htmx
      - content type of server-sent event is `text/event-stream`
      - htmx will replace element with result of server payload
      - server-sent event protocol denotes end of message with `\n\n`
  - github.com/PyHAT-stack/web-async-patterns
- Django TLDR
  - Use `StreamingHttpResponse` to stream critical elements to user asap
  - Use HTML fragments with HTMX to update parts of page
  - leverage frameworks like AlpineJS to power rich interactions