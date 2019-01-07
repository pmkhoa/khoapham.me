---
title: Middleman Localization
date: 2013-06-05
draft: true
tags: [Web Development, Middleman]
---

Recently when developing a site using middleman, my client wonder that if we can do localization for the website. Fortunately, Middleman comes with the localization extension i18n which is pretty simple to integrate it into the site. 
<!--more-->
Although there's a quick overview of Middleman Localization available on the <a href="http://middlemanapp.com/" target="_blank">middlemanapp.com</a>, I still find it a little bit confused for Middleman beginners. Here are quick steps how to set it up:

<strong>1. Activate i18n and update your bundle.</strong>

To activate, going to your config.rb file, then add at the end of file.
<pre>activate :i18n</pre>
Open terminal and direct to your middleman app, then run.
<pre>$ bundle update</pre>

<strong>2. Organize your middleman folders so that it knows where translated files located.</strong>

- All the translated files should be located inside "locales" folders, in your root directory of your source code. For example, the structure of my directory:
<pre>source
build
config.rb
Gemfile
Gemfile.lock
locales</pre>
- Then you need to put all your template files in one folder called localizable.

<img style="border: 1px solid #d4d4d4;" alt="Localizable" src="/img/Localizable-1024x337.png" width="1024" height="337" />

<strong>3. Add translation text into your translated files:</strong>

- Go to your "locales" folder, and create files called: en.yml, and es.yml, these will include all translation text of English and Spanish for your site.

For example, in my en.yml file, it contains:
<pre>---
en:
  siteName: "Khoa Pham"
  footer:
    privacy: "Privacy"
    legal_disclosure: "Legal Disclosure"
    terms_of_use: "Terms of Use"
    copyright: "Copyright"</pre>

- In my es.yml, it contains:
<pre>---
en:
  siteName: "Khoa Pham"
  footer:
    privacy: "Privacidad"
    legal_disclosure: "Aviso legal"
    terms_of_use: "Términos de uso"
    copyright: "Copyright"</pre>

<strong>4. Output translation text to your template files.</strong>

Since you already put your template files into the localizable folders, now you can go to files in that folder and output localizable text. My site only has one template file called index.html.erb:


To output translated text from the translated files, we use: &lt;%= I18n.t("your_key_here") %&gt;. For example,
<pre>&lt;%= I18n.t("siteName") %&gt;
&lt;%= I18n.t("footer.privacy") %&gt;
&lt;%= I18n.t("footer.legal_disclosure") %&gt;
&lt;%= I18n.t("footer.terms_of_use") %&gt;
&lt;%= I18n.t("footer.copyright") %&gt;</pre>

You can view your site under Spanish by going to: http://localhost:4567/es/index.html
