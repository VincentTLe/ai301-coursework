I opened a fix for a bug in pyfixest, an open-source Python library used for statistical analysis. I worked on making one of its statistical checks support weighted data, where some observations count more than others.

The original function stopped with an error when weights were used. During testing, I found that simply removing the error was not enough: a faster calculation could still produce a different result from the slower, more direct method. I updated the calculation and added tests to check that both methods agree.

I also changed my original testing plan after discovering that the external tool I intended to compare against was not using weights in the same way. That experience reminded me to question a reference result instead of assuming it must be right.

The pull request is still open as a draft and has not been reviewed or merged. I have also worked on keeping it current as the project changes. This contribution taught me that reliable software requires more than making an error disappear; it requires checking what happens afterward.

https://github.com/py-econometrics/pyfixest/pull/2024
#OpenSource #Python #SoftwareEngineering
