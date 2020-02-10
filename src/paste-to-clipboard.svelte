<script context="module">
  export function pasteToClipboard(text) {
    const tempTextInput = document.createElement("input");
    tempTextInput.type = "text";
    document.body.appendChild(tempTextInput);
    tempTextInput.value = text;
    tempTextInput.select();
    document.execCommand("copy");
    document.body.removeChild(tempTextInput);
  }
</script>
