TLDR: Use Mac TextEdit with the entire document set to LTR but the "selection" set to RTL.

This is a real pain, particularly when trying to insert English to the `ar.json` file:

```
{
  content: "<p>(Arabic and english)</p>"
}
```

First off, I find the best editor to use on Mac is the default text editor (TextEdit) **in plaintext mode**.

The issue here as I understand it is that the JSON document needs to be in LTR mode but the sentences are written in RTL. The reason the JSON document needs to be in LTR mode is that most text editors can't render the JSON structure nor the HTML tags in RTL correctly. Even though the editor is in LTD mode, the Arabic sentences themselves are saved RTL. This means that the second you try to copy/paste into the file, or edit any of the sentences, it will mess up, as the editor is trying to write in LTR but the sentences are originally RTL.

To get around this and cleanly edit the text in the file, it's easiest to construct the translations in a separate blank document. Unlike the JSON, this blank document should be in RTL mode. TextEdit allows you to set both the "document" direction as well as the "selection" direction by selecting "Text" > "Writing Direction" in the toolbar. Make sure both of these are "Right to left". With that done you can edit/write new sentences before transferring them to the JSON.

The important part is when you go to paste back to the JSON. Although you keep the JSON document in LTR, you need to place the cursor where you want to paste and then change the "Selection" text direction from above to "Right to left". In other words, the document stays LTR but the selection/paste goes to RTL. Then you should be able to insert correctly.