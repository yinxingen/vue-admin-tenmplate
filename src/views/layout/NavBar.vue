<template>
    <div class="side-nav">
        <el-menu router ref="navbar" :default-active="defActive" menu-trigger="click" @select="selectMenu" unique-opened>
            <nav-bar-item v-for="(item, n) in navList" :item="item" :navIndex="String(n)" :key="n"></nav-bar-item>
        </el-menu>
    </div>
</template>

<script>
import { mapState } from 'vuex'
import NavBarItem from './NavBarItem'

export default {
    components: { NavBarItem },
    data() {
        return {

        }
    },
    computed:{
        ...mapState({
            navList: state => state.auth.navList
        }),
        defActive(){
            return this.$route.path
        },
    },
    watch: {
        // 当通过TagNav来激活页面时也执行一次selectMenu
        $route(){
            let path = this.$route.path
            let indexPath = this.$refs.navbar.items[path].indexPath
            this.selectMenu(path, indexPath)
        }
    },
    methods: {
        // eslint-disable-next-line
        selectMenu(index, indexPath){
            /**
             * 在选择父级菜单时自动关闭其下所有子菜单
             * 选择时获取点击菜单的父级index，并计算得到该index在已打开菜单中的索引值，
             * 关闭位于当前打开菜单中该索引值之后的全部菜单
             */
            let openedMenus = this.$refs.navbar.openedMenus
            let openMenuList
            // 如果点击的是二级菜单，则获取其后已经打开的菜单
            if(indexPath.length > 1){
                let parentPath = indexPath[indexPath.length-2]
                openMenuList = openedMenus.slice(openedMenus.indexOf(parentPath)+1)
            } else{
                openMenuList = openedMenus
            }
            
            // 关闭菜单
            openMenuList = openMenuList.reverse()
            openMenuList.forEach((ele) => {
                this.$refs.navbar.closeMenu(ele)
            })
        },
    },
}
</script>

<style scoped lang="scss">
      
    .side-nav{
        // float: left;
        // width: 150px;
        position: absolute;
        top: 50px;
        bottom: 0;
        left: 0;
        width: 160px;
        background-color: #f4f4f4;
        border-right: 1px solid #ccc;
        overflow: auto;
        box-sizing: border-box;
    }
    
</style>


