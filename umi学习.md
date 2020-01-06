## umi学习

#### coding splitting （代码拆分）

应用进行bundle打包固然是好的，但随着应用的逐步增长，则打包的文件也会越来越庞大，特别是对于引用的三方库。你打包时需要时刻注意这些三方库代码，以免在不经意间使得打包文件过大而造成应用加载的缓慢。

为了避免打包后的文件臃肿，我们推荐的解决方式是在代码中使用”splitting”。Code-Splitting可以打出多个bundle，应用可以根据此时的需要进行实时bundle加载，目前Webpack和Browserify（通过factor-bundle方式）均已支持Code-Splitting。

Code-splitting通过按需加载的方式可以大幅提高应用的体检。 而且你不需要对你的代码进行删减，你可以避免加载你并不需要的代码，还可以减少初次加载的代码。

