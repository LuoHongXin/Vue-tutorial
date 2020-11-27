[toc]
## 全局 CLI 配置
有些针对 `@vue/cli` 的全局配置，可以使用 `vue config` 命令来审查或修改全局的 CLI 配置。

## vue.config.js
`vue.config.js` 是一个可选的配置文件，如果项目的根目录（和 `package.json` 同级的）存在这个文件，那么它会被 `@vue/cli-service` 自动加载。你也可以使用 `package.json` 中的 `vue` 字段，但是只能用 JSON 格式来写。
这个文件最后导出一个包含选项的对象：
```js
// vue.config.js
module.exports = {
    // 选项...
}
```
#### publicPath
- type: `string`
- default: `/`
部署应用包时的基本 URL。用法和 webpack 本身的 `output.publicPath` 一致，但是 Vue CLI 在一些其他地方也需要用到这个值，所以请使用 `publicPath` 而不是修改 webpack 的 `output.publicPath`
默认情况下，Vue CLI 会假设你的应用是被部署在一个域名的根路径上，例如：`http//www.myapp.com/` 如果应用被部署在一个子路径上，你就需要用这个选项指定这个子路径，例：若部署在 `https//www.myapp.com/myapp/`，则设置 `publicPath` 为 `/myapp/`。
这个值也可以被设置为空字符串（`''`）或是相对路径（`'./'`），这样所有的资源都会被链接为相对路径，这样打出来的包可以被部署在任意路径，也可以用在类似 Cordova hybrid 应用的文件系统中。 
<div style="background-color:rgba(255,229,100,.3);padding:10px 20px">
相对 publicPath 的限制<br>
相对路径的 <code>publicPath</code> 有一些使用上的限制。在以下情况，应避免使用相对 <code>publicPath</code>:
    <ul>
        <li>
           当前基于 HTML5 <code>history.pushState</code> 的路由时；
        </li>
        <li>
          当使用 <code>pages</code> 选项构建多页面应用时。
        </li>
    <ul>
</div>

#### outputDir
- type：`string`
- default：`dist`
当运行 `vue-cli-service bulid` 时生成的生成环境构建文件的目录。注意目标目录在构建之前会被清除（构建时传入 `--no-clean` 可关闭该行为）。
请始终使用 `outputDir` 而不要修改 webpack 的 `output.path`。

#### assetsDir
- type：`string`
- default：`''`
放置生成的静态资源（js、css、img、fonts）的（相对于 `outputDir`）目录。从生成的资源覆写 filename 或 chunkFilename 时，`assetsDir` 会被忽略。

#### indexPath
- type：`string`
- default：`index.html`
指定生成的 `index.html` 的输出路径（相对于 `outputDir`）。也可以是绝对路径。

#### filenameHashing
- type：`boolean`
- default：`true`
默认情况下，生成的静态资源在它们的文件名中包含了 hash 以便更好的控制缓存。然而，这也要求 index 的 HTML 是被 Vue CLI 自动生成的。如果你无法使用 Vue CLI 生成的 index HTML，你可以通过将这个选项设为 false 来关闭文件名的哈希。

#### pages
- type：`Object`
- default：`undefined`
Vue 项目默认是单页面应用，但是设置这个选项之后，Vue 可以作为多页面应用去使用。在 multi-page 模式下构建应用。每个“page”应该有一个对应的 JavaScript 入口文件。其值应该是一个对象，对象的 key 是入口的名字，value是：
  * 包含 `entry`、`template`、`filename`、`title`、`chunks` 的对象（除了 `entry` 之外都是可选的）；
  *  或一个指定其 `entry` 的字符串。
    ```js
      module.exports = {
          pages: {
              // 对象的 key 入口的名字 index
              index: {
                 // page 的入口
                 entry: 'src/index/main.js',
                 // 模板来源
                 template: 'public/index.html',
                 // 在 dist/index.html 的输出
                 filename: 'index.html',
                 // 当使用 title 选项时，
                 // template 中的 title 标签需要是 <title><%= htmlWebpackPlugin.option.title %></title>
                 title: 'Index.Page',
                 // 在这个页面中包含的块，默认情况下会包含
                 // 提取出来的通用 chunk 和 vendor chunk
                 chunks: ['chunk-vendors','chunk-common','index']
              },
              // 当使用只有入口的字符串格式时，
              // 模板会被推导为 'public/subpage.html'
              // 并且如果找不到的话，就回退到 'public/index.html'.
              // 输出文件名会被推导为 'subpage.html'
              subpage: 'src/subpage/main.js'
          }
      }
    ```
    当在 multi-page 模式下构建时，webpack 配置会包含不一样的插件（这时会存在多个 `html-webpack-plugin` 和 `preload-webpack-plugin` 的实例）。如果你试图修改这些插件的选秀权，请确认运行 `vue inspect`。

#### runtimeCompiler
- type：`boolean`
- default：`false`
是否使用包含运行时编译器的 Vue 构建版本。设置为 true 后你就可以在 Vue 组件中使用 `template` 选项，但是这个会让你的应用额外增加 10kb 左右。

#### productionSourceMap
- type：`boolean`
- default：`true`
如果你不需要生产环境的 source map，可以将其设置为 false 以加速生产环境的构建。

#### configureWebpack
- type：`Object | Function`
如果这个值是一个对象，则会合并到最终的webpack配置中。
如果这个值是一个函数，则会接收被解析的配置作为参数。该函数既可以修改配置并不返回任何东西，也可以返回一个被克隆或合并过的配置版本。
```js
    configureWebpack: {
        resolve: {
            alias: {
                '@': path.resolve(__dirname, 'src'),
                '@views': path.resolve(__dirname, 'src/app/views'),
                '@common': path.resolve(__dirname, 'src/common'),
                '@constants': path.resolve(__dirname, 'src/constants'),
                '@utils': path.resolve(__dirname, 'src/common/utils')
            }
        },
        plugins: [
            new webpack.ProvidePlugin({
                _: 'lodash',
                Axios: 'axios'
            }),
            new EcpVersionWebpackPlugin(),
            // new EcpThemeWebpackPlugin({
            //     entry: {
            //         default: './src/theme/default/index.scss',
            //         darken: './src/theme/darken/index.scss'
            //     }
            // })
            // 配置compression-webpack-plugin压缩
            new CompressionWebpackPlugin({
                algorithm: 'gzip',
                test: new RegExp('\\.("js|css")$'),
                threshold: 10240,
                minRatio: 0.8
            })
        ],
        performance: {
            hints: 'warning',
            // 入口起点的最大体积 整数类型（以字节为单位）
            maxEntrypointSize: 50000000,
            // 生成文件的最大体积 整数类型（以字节为单位 300k）
            maxAssetSize: 30000000,
            // 只给出 js 文件的性能提示
            assetFilter: function (assetFilename) {
                return assetFilename.endsWith('.js');
            }
        }
        // devtool: '#eval-source-map'
    },
```
#### chainWebpack
- type：`Function`
是一个函数，允许对内部的 webpack 配置进行更细粒度的修改。
```js
 chainWebpack: config => {
        config.plugins.delete('prefetch');
        if (process.env.NODE_ENV === 'production') {
            config.plugin('compression').use(CompressionWebpackPlugin, [{
                test: /\.js$|\.css$/,
                algorithm: 'gzip',
                threshold: 1024 * 512
            }]);
        }
    },
```

#### css
css 下有很多配置，像是 modules 、requireModuleExtension 、 extract 、loaderOptions 等
```js
css: {
        loaderOptions: {
            sass: {
                data: '@import "~ecp-ui/styles/var";'
            }
        }
    },
```

#### devServe
一般用来做服务器代理的设置，其下也有很多配置，像是 port、host、https、open、proxy等
```js
devServer: {
        port: 8080,
        host: '0.0.0.0',
        https: false,
        open: true,
        proxy: proxyConfig
    },
```

#### pluginOptions
这是一个不进行任何 schema 验证的对象，因此它可以用来传递任何第三方插件选项。
```js
pluginOptions: {
  foo: {
    // 插件可以作为 'options.pluginOptions.foo'访问这些选项。
  }
}
```
