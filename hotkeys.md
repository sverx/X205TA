Some hotkeys (put to sleep `<Fn>+<F1>`, airplane mode toggle `<Fn>+<F2>`, screen brightness decrease/increase `<Fn>+<F5>/<F6>`) might not work out-of-the-box.

In this case just installing the `acpi-support` module might fix at least some of those.

Run:

```
sudo apt-get install acpi-support
```

original source: https://packages.debian.org/sid/acpi-support

