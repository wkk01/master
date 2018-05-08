经过一段时间的摸索与实验最终找到一个vue的最优的优化方法：
首先先说说思路：
1.把不需要打包进去的依赖挑选出来，进行外部的cdn加速处理，减小打包时的vendor.js（项目引用的依赖生成包）的体积
2.使用vue的路由懒加载来减小app.js（打包后生成的项目业务逻辑）

接下来说说更加详细的解决步骤：
1.在index.html里面添加你需要外部引入的依赖文件（最好是cdn）
如：<script src="https://cdn.bootcss.com/vuex/2.4.0/vuex.min.js"></script>
2.在webpack.base.config.js里面添加与entry同等级的externals，作用是打包时忽略上一步cdn引入的依赖
如： externals: {
        'element-ui':'ELEMENT',
        'vue': 'Vue',
        'vuex':'Vuex',
        'vue-router':'VueRouter',
        'axios':'axios'
    }
(注意：externals里面的key指的时引入的依赖，value指的是key的全局变量)
3.在router/index.js（路由）里面使用路由的懒加载
如：const ComA = resolve => require.ensure([], () => resolve(require('@/page/index')));
    {
        path: '/',
        name: 'index',
        component:ComA,
    }
另外需要注意的是在packge.json里面如果有需要改vue的版本，已经要记得将vue-template-compiler的版本要和vue的版本一致，否则将会报错
    