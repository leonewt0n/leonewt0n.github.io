---
layout: post
title: "Setting Up a Simple Github Pages Jekyll Blog"
author: "Google Gemini"
---

### The "No Software" Solution

The absolute simplest software you can use is **none at all**. Because your goal is to write directly in GitHub's web-based Markdown editor, you can rely entirely on GitHub Pages' built-in processing engine (Jekyll). You do not need to install any frameworks, static site generators, or command-line tools.

Here is the exact setup to achieve your browser-only workflow.

---

### Phase 1: Repository Setup

1. **Create the Repository:** Go to GitHub and create a new public repository named exactly `your-username.github.io`.
2. **Enable Pages:** Go to the repository's **Settings** > **Pages**. Ensure the build source is set to **Deploy from a branch** and select your `main` branch.
3. **Establish the Homepage:** To display a list of your articles, create a file named `index.html` in the root of your repository and paste this minimal code:

{% raw %}
```html
<!DOCTYPE html>
<html>
<body>
  <h1>My Blog</h1>
  <ul>
    {% for post in site.posts %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
</body>
</html>
```
{% endraw %}

4. **Create the Posts Directory:** Create a new folder in your repository named `_posts`. GitHub requires this specific folder name to automatically recognize and render your articles.

---

### Phase 2: Cloudflare DNS Configuration

To route your custom domain through Cloudflare to GitHub Pages, you need to configure your DNS records.

1. **Log in to Cloudflare** and navigate to your domain's **DNS** settings.
2. **Create A Records:** Add four separate **A** records for your apex domain (e.g., `yourdomain.com`). Set the **Name** to `@` and point them to GitHub's IP addresses:
   * `185.199.108.153`
   * `185.199.109.153`
   * `185.199.110.153`
   * `185.199.111.153`
3. **Create a CNAME Record:** Add a **CNAME** record for the `www` subdomain. Set the **Name** to `www` and the **Target** to `your-username.github.io`.
4. **SSL/TLS Settings:** Navigate to Cloudflare's **SSL/TLS** tab and set the encryption mode to **Full (Strict)**. This ensures secure end-to-end communication.
5. **Add Domain to GitHub:** Go back to your GitHub repository's **Settings** > **Pages**. Under the "Custom domain" section, enter your domain (e.g., `yourdomain.com`) and click **Save**. GitHub will automatically generate a `CNAME` file in your repository.

---

### Phase 3: Publishing with Base64 Images

You are now ready to publish entirely through the web interface.

1. **Write an Article:** Inside your `_posts` folder on GitHub, click **Add file** > **Create new file**.
2. **Name the File:** You **must** use this strict naming convention for the engine to process it as a blog post: `YYYY-MM-DD-title-of-post.md` (e.g., `2026-06-19-my-first-post.md`).
3. **Add Front Matter:** At the very top of the Markdown editor, add a simple YAML block to define the title of the article:

```markdown
---
title: "My First Post"
---
```

4. **Embed Base64 Images:** Write your content using standard Markdown. When you need to insert your base64 encoded image, use the standard Markdown image syntax, replacing the traditional URL with the base64 data string.

**Syntax:**
```markdown
![Alt text describing the image](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...)
```

> **Important Note:** Base64 strings are massive blocks of text. It is highly recommended to aggressively compress your images before encoding them. Otherwise, your Markdown file will become incredibly long, sluggish, and difficult to edit inside the browser.

5. **Publish:** Click **Commit changes**. GitHub's background processes will automatically build and publish your new article to your custom domain within a minute or two.

### Phase 4: Google Analytics:
To add modern Google Analytics (GA4) to the Hacker theme using only the GitHub web interface, you will use the theme's built-in header override feature.

While some newer themes allow you to simply drop an ID into your `_config.yml`, Hacker's default configuration was built for an older version of Google Analytics that is now deprecated. Injecting the raw HTML snippet directly into a custom include file ensures your tracking works perfectly with Google's latest standards.

### Step 1: Get your Tracking Snippet

1. Create a property in your Google Analytics account.
2. Navigate to your **Web Stream Details**.
3. Copy the entire **Global site tag (gtag.js)** HTML snippet. It will look something like this:
```markdown
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>

```



### Step 2: Inject it into GitHub

1. Open your repository on GitHub.
2. Click **Add file** > **Create new file**.
3. In the file name box, type `_includes/head-custom-google-analytics.html`.
*(Note: Typing the forward slash `/` will automatically create the `_includes` folder for you).*
4. Paste your exact Google Analytics HTML snippet into the main text area.
5. Click **Commit changes**.

GitHub's background engine will rebuild your site and automatically inject this tracking script into the `<head>` of your homepage and every single blog post you publish. You should start seeing live traffic data in your Google Analytics dashboard within a few minutes of your next deployment.
