<template>
    <el-container class="center-container">
        <el-header>
            <el-menu class="nav-bar" mode="horizontal" :default-active="activeIndex" @select="handleSelect">
                <el-menu-item index="1">Recommand</el-menu-item>
                <el-menu-item index="2">Favorites</el-menu-item>
                <el-menu-item index="3">Account</el-menu-item>
            </el-menu>
        </el-header>

        <el-main>


            <div class="content" v-if="activeIndex === '1'">
                <div ref="customRadioGroup" v-if="activeIndex === '1'" class="custom-radio-group">
                    <div :class="['custom-radio-item', selectedMainOption === 'option1' ? 'active' : '']"
                        @click="changeSelectedMainOption('option1')">
                        Character
                    </div>
                    <div :class="['custom-radio-item', selectedMainOption === 'option2' ? 'active' : '']"
                        @click="changeSelectedMainOption('option2')">
                        Abstract
                    </div>
                    <div :class="['custom-radio-item', selectedMainOption === 'option3' ? 'active' : '']"
                        @click="changeSelectedMainOption('option3')">
                        All-purpose
                    </div>
                </div>

                <transition name="slide">
                    <div ref="subOptions" class="sub-options" v-if="showSubOptions">
                        <div :class="['custom-radio-item', selectedSubOption === option ? 'active' : '']"
                            v-for="option in subOptions" :key="option" @click="changeSelectedSubOption(option)">
                            {{ option }}
                        </div>
                    </div>
                </transition>

                <transition name="slide">
                    <div ref="subSubOptions" class="subSub-options" v-if="subSubOptions.length > 0">
                        <div :class="['custom-radio-item', selectedSubSubOption === option ? 'active' : '']"
                            v-for="option in subSubOptions" :key="option" @click="changeSelectedSubSubOption(option)">
                            {{ option }}
                        </div>
                    </div>
                </transition>
                <div ref="container">
                    <div class="image-container" v-for="image in filteredImages" :key="image.url" @click="toggleMark(image)"
                        :class="{ marked: isMarked(image) }">
                        <img :src="image.url" @load="onImageLoad" class="image" />
                    </div>
                </div>
            </div>

            <div v-if="activeIndex === '2'">
                <div v-if="isLoggedIn">
                    <div ref="container" class="content">
                        <div class="image-container" v-for="image in markedImages" :key="image.url"
                            @click="toggleMark(image)" :class="{ marked: isMarked(image) }">
                            <img :src="image.url" @load="onImageLoad" class="image" />
                        </div>
                    </div>
                </div>
                <div v-else>
                    <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
                        <span>Please log in</span>
                        <span slot="footer" class="dialog-footer">
                            <el-button @click="goToLogin">verify</el-button>
                        </span>
                    </el-dialog>
                </div>

            </div>

            <div v-if="activeIndex == '3'">
                <el-card shadow="hover">
                    <div slot="header" class="clearfix">
                        <span>Account</span>
                    </div>
                    <div v-if="!isLoggedIn">
                        <el-input v-model="account.username" placeholder="Username"></el-input>
                        <el-input v-model="account.password" placeholder="Password" type="password"></el-input>
                        <el-button @click="login(account.username, account.password)">Login</el-button>
                        <p class="error" v-if="loginError">{{ loginError }}</p>
                    </div>

                    <div v-else>
                        <el-avatar :size="80" :src="account.avatar"></el-avatar>
                        <div v-if="!editable">
                            <p>Name: {{ account.name }}</p>
                            <p>Gender: {{ account.gender }}</p>
                            <p>Birthday: {{ account.birthday }}</p>
                            <p>Email: {{ account.email }}</p>
                            <p>Signature: {{ account.signature }}</p>
                            <el-button @click="editProfile">Edit</el-button>
                            <el-button @click="logout">Logout</el-button>
                        </div>
                        <div v-else>
                            <el-input v-model="account.name" placeholder="Name"></el-input>
                            <el-input v-model="account.gender" placeholder="Gender"></el-input>
                            <el-input v-model="account.birthday" placeholder="Birthday"></el-input>
                            <el-input v-model="account.email" placeholder="Email"></el-input>
                            <el-input v-model="account.signature" placeholder="Signature"></el-input>
                            <el-button @click="saveProfile">Save</el-button>
                            <el-button @click="cancelEdit">Cancel</el-button>
                        </div>
                    </div>
                </el-card>
            </div>
        </el-main>
    </el-container>
</template>
  
<script>
export default {

    data() {
        return {
            activeIndex: '1',
            selectedMainOption: '',
            showSubOptions: false,
            subOptions: [],
            selectedSubOption: '',
            subSubOptions: [],
            selectedSubSubOption: '',
            isLoggedIn: false,
            loginError: '',
            account: {
                username: 'admin',
                password: '123456',
                avatar: 'https://example.com/avatar.jpg',
                name: 'John Doe',
                gender: 'Male',
                birthday: '1990-01-01',
                email: 'john.doe@example.com',
                signature: 'Hello World!'
            },
            editable: false,
            accountCopy: {},
            dialogVisible: false,
            images: [
                { url: 'https://img0.baidu.com/it/u=1225235866,1923876974&fm=253&fmt=auto&app=138&f=JPEG?w=521&h=500', tag: 'Doraemon' },
                { url: 'https://inews.gtimg.com/newsapp_bt/0/14438256812/1000', tag: 'Doraemon' },
                { url: 'https://img1.baidu.com/it/u=3714752597,145519275&fm=253&fmt=auto&app=138&f=GIF?w=366&h=282', tag: 'pokemon' },
                { url: 'http://img0.baidu.com/it/u=3833714177,1794571311&fm=253&app=138&f=JPEG?w=520&h=500', tag: 'Doraemon' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2Ff5fc6fdf-885d-41b9-8970-6601ad2dfaf4%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697260789&t=a1c67eb487b0be992f9a61c2f85e3265', tag: 'Doraemon' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2F318d5617-1d7f-45e8-bd25-5bf8fe985b9b%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697260803&t=154a87094bc001895563b859d93d960e', tag: 'Doraemon' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2F7cac3d51-1d5a-441d-8e2a-117a6ec5c15f%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697260803&t=617d744103356c5a8e0050cbdbe09ee5', tag: 'Doraemon' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2F685c36a4-59ef-4d3b-87d1-aa2857c98a6e%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697260803&t=4f9f1af6e7bc93c9d9e9fd0351e43636', tag: 'Doraemon' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2F0e74b88c-0637-4108-a8b5-c7f0d3780ac8%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697260803&t=5bf89e8a188878df6d973ff27b8876f3', tag: 'Doraemon' },
                { url: 'https://img0.baidu.com/it/u=1473414627,2491967021&fm=253&fmt=auto&app=138&f=JPEG?w=200&h=200', tag: 'Pokemon' },// ...其他图片
                { url: 'https://i.17173cdn.com/2fhnvk/YWxqaGBf/cms3/doAsJfbnBibcigb.jpg', tag: 'pokemon' },
                { url: 'https://img1.gtimg.com/comic/pics/hv1/105/218/2217/144216120.jpg', tag: 'pokemon' },
                { url: 'https://alioss.yystv.cn/news/36884493fd37360363a170231e3912c1.jpg_mw680', tag: 'pokemon' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fc-ssl.duitang.com%2Fuploads%2Fitem%2F202007%2F15%2F20200715124035_kiixy.thumb.1000_0.jpg&refer=http%3A%2F%2Fc-ssl.duitang.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697269939&t=a5179add5e35354f86135901ab618aee', tag: 'Crayon Shin Chan' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2Fb555f62b-5f2f-429a-ab14-824803811cd4%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697269990&t=2d930ef339709af189112099994c4077', tag: 'Crayon Shin Chan' },
                { url: 'https://img2.baidu.com/it/u=808631844,783185956&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=500', tag: 'Crayon Shin Chan' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fc-ssl.duitang.com%2Fuploads%2Fitem%2F202003%2F27%2F20200327201233_L4f3B.thumb.400_0.jpeg&refer=http%3A%2F%2Fc-ssl.duitang.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697270217&t=6a29118439676169ae7a543ead7a043b', tag: 'Crayon Shin Chan' },
                { url: 'https://img1.baidu.com/it/u=1633699381,957019754&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=502', tag: 'Bocchi the Rock' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2F659a3b3d-530f-4e31-b8dc-7080bda80f15%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697270361&t=887c63df7ebb51d803941f1e7a4f5b1a', tag: 'Bocchi the Rock' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2Fa3c4bec6-f655-46c8-a905-3c57ec315e6c%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697270361&t=930e34ce2eff08fcc90030444d5e0626', tag: 'Bocchi the Rock' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2F81344020-e86a-41e1-aafe-d61803a7b9b8%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697270674&t=be9d38d81194f53a3b729f1e933cb7cf', tag: 'Bocchi the Rock' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2Fe28bc6cc-c07c-4102-9581-927857943eca%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697270674&t=ef8553694df842bd53b8055eed7d6e29', tag: 'Bocchi the Rock' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2F58451ccf-8e15-4906-b5c9-5a81e2143c89%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697270820&t=ec39d1f132c9dc45f53a2b0339863c7a', tag: 'Ania' },
                { url: 'https://i1.hdslb.com/bfs/archive/97e51d85a0711350fc013c7710dfac65eed21ec5.jpg', tag: 'Ania' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsafe-img.xhscdn.com%2Fbw1%2F11e328fc-0d09-4495-86e0-1e87c8264353%3FimageView2%2F2%2Fw%2F1080%2Fformat%2Fjpg&refer=http%3A%2F%2Fsafe-img.xhscdn.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697270900&t=79f870e916a69c2972c82282ccf2677f', tag: 'Ania' },
                { url: 'https://i2.hdslb.com/bfs/archive/a49683cfed4964a7c7fd7ad9436f370fe6d19ddc.jpg', tag: 'Ania' },
                { url: 'https://inews.gtimg.com/newsapp_bt/0/13305437216/1000', tag: 'melon-eating masses' },
                { url: 'https://img1.baidu.com/it/u=1639651978,2497061519&fm=253&fmt=auto&app=138&f=JPEG?w=277&h=284', tag: 'melon-eating masses' },
                { url: 'https://img0.baidu.com/it/u=807828283,3298113336&fm=253&fmt=auto&app=138&f=JPG?w=640&h=432', tag: 'melon-eating masses' },
                { url: 'https://img0.baidu.com/it/u=62573467,2968581915&fm=253&fmt=auto&app=138&f=JPG?w=640&h=438', tag: 'melon-eating masses' },
                { url: 'https://tiebapic.baidu.com/forum/pic/item/70367cfe9925bc31097933ab18df8db1cb137024.jpg?tbpicau=2023-09-16-05_00b5bd7fde62453a588ec27866be3ea3', tag: 'Attack Post Owner' },
                { url: 'https://tiebapic.baidu.com/forum/w%3D580%3B/sign=21209f4866a446237ecaa56aa819720e/342ac65c10385343b7f06213d513b07eca8088c1.jpg?tbpicau=2023-09-16-05_07138e6c4cea2deb0acd731a58d1d5ad', tag: ' Attack Post Owner' },
                { url: 'https://tiebapic.baidu.com/forum/w%3D580%3B/sign=714b0a01d78fa0ec7fc7640516ac58ee/2934349b033b5bb5da71e8cc70d3d539b600bc5a.jpg?tbpicau=2023-09-16-05_a9c37b5005402d5c0244f1ba8166a587', tag: ' Attack Post Owner' },
                { url: 'https://tiebapic.baidu.com/forum/w%3D580%3B/sign=202682951fee3d6d22c687c3732d6d22/14ce36d3d539b6004be89e85af50352ac65cb7da.jpg?tbpicau=2023-09-16-05_78b745f85e801da816593263e5c1e4d1', tag: ' Attack Post Owner' },
                { url: 'http://tiebapic.baidu.com/forum/w%3D580/sign=9fecf719db16fdfad86cc6e6848f8cea/7586301fd21b0ef423d6946c9bc451da81cb3e8f.jpg?tbpicau=2023-09-16-05_aacc8fb761f2e23cfaf53e59d30e29be', tag: 'Sun Xiaochuan' },
                { url: 'http://tiebapic.baidu.com/forum/w%3D580/sign=064a5def68738bd4c421b239918a876c/4c26907a02087bf4d62f75dbb4d3572c10dfcf72.jpg?tbpicau=2023-09-16-05_e710fb647c7a53e00f66031ab5535a1e', tag: 'Sun Xiaochuan' },
                { url: 'http://tiebapic.baidu.com/forum/w%3D580/sign=82cc0ff777adcbef01347e0e9caf2e0e/55699b504fc2d562d75be6dea11190ef76c66c07.jpg?tbpicau=2023-09-16-05_05be48073cc1ef31347844c2b212111a', tag: 'Sun Xiaochuan' },
                { url: 'http://tiebapic.baidu.com/forum/w%3D580/sign=a393cc28805c1038247ececa8210931c/e0a531adcbef760901f555ad68dda3cc7dd99edc.jpg?tbpicau=2023-09-16-05_a27248402cc89ceae845c3e31c2b1260', tag: 'Sun Xiaochuan' },
                { url: 'https://pic1.zhimg.com/80/v2-ba2e778a4dae7e437d68fdd068e621cd_720w.webp?source=1940ef5c', tag: 'Tenzing Tsondu' },
                { url: 'https://pic1.zhimg.com/80/v2-b1d48cdfb21c3eae7e0b0ec4dc3d7730_720w.webp?source=1940ef5c', tag: 'Tenzing Tsondu' },
                { url: 'https://pic1.zhimg.com/80/v2-0a36fb5793e6fb337ccd629224c1d103_720w.webp?source=1940ef5c', tag: 'Tenzing Tsondu' },
                { url: 'https://pica.zhimg.com/80/v2-4cb113ddb95be32a0176df9ace625e0f_720w.webp?source=1940ef5c', tag: 'Tenzing Tsondu' },
                { url: 'https://img1.baidu.com/it/u=1349301868,240412324&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=500', tag: 'Wang Jingze' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fb-ssl.duitang.com%2Fuploads%2Fitem%2F201809%2F16%2F20180916234253_jQZEY.jpeg&refer=http%3A%2F%2Fb-ssl.duitang.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697297135&t=4f0bafec1bceb1b1f53774fd7c642210', tag: 'Wang Jingze' },
                { url: 'https://img2.baidu.com/it/u=3692558525,213442176&fm=253&fmt=auto&app=138&f=JPEG?w=440&h=440', tag: 'Wang Jingze' },
                { url: 'https://wx3.sinaimg.cn/large/415f82b9ly1fs2uuu0pojj20q70ojn1y.jpg', tag: 'Wang Jingze' },
                { url: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fc-ssl.duitang.com%2Fuploads%2Fitem%2F201802%2F25%2F20180225202504_LVvdZ.thumb.400_0.jpeg&refer=http%3A%2F%2Fc-ssl.duitang.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1697298787&t=36d2750b095c849a2b87b370d96782c0', tag: 'POPPY PAPPY DAY' },
                { url: 'https://img2.baidu.com/it/u=2433232269,1592730969&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=500', tag: 'POPPY PAPPY DAY' },
                { url: 'https://img2.baidu.com/it/u=508686867,935379201&fm=253&fmt=auto&app=138&f=JPEG?w=531&h=500', tag: 'POPPY PAPPY DAY' },
                { url: 'https://img1.baidu.com/it/u=1170068939,2895082408&fm=253&fmt=auto&app=138&f=PNG?w=400&h=399', tag: 'POPPY PAPPY DAY' },
                { url: 'https://img.tuguaishou.com/designer_upload_asset/15/91/45/18/77/c1/c151c45c660e1d9a3da5cb43be309f2d.png!w300_w?auth_key=2237990400-0-0-ca34c4294f595262f75d4b68d315a34b', tag: 'Anger' },
                { url: 'https://img1.baidu.com/it/u=1797795117,3248710643&fm=253&fmt=auto&app=138&f=JPEG?w=373&h=500', tag: 'Anger' },
                { url: 'https://bpic.588ku.com/art_pic/19/05/28/5219d0da51f3f6368c6b6d0511f30a86.jpg%21/fw/253/quality/90/unsharp/true/compress/true', tag: 'Anger' },
                { url: 'https://img0.baidu.com/it/u=2676242432,670472302&fm=253&fmt=auto&app=138&f=JPEG?w=499&h=500', tag: 'Anger' },
                { url: 'https://ts1.cn.mm.bing.net/th/id/R-C.4a4cc45329866b3c2ddd830a29a42084?rik=ip2kCiHTTLr4VA&riu=http%3a%2f%2fwww.lrioh.com%2fupload%2fimages%2f2017%2f5%2f81872244.jpeg&ehk=3ZmPHEgXaUmznYbodAiSdwAJm6tPm8tcO%2bXlDddDA9c%3d&risl=&pid=ImgRaw&r=0', tag: 'grief' },
                { url: 'https://pic2.zhimg.com/50/v2-3855d028ee0cc4da37e4a8abbc9e23b9_720w.jpg?source=1940ef5c', tag: 'grief' },
                { url: 'https://pic1.zhimg.com/v2-225619de6620b18a1efdc10a2a4fd3d1_r.jpg', tag: 'grief' },
                { url: 'https://ts1.cn.mm.bing.net/th/id/R-C.c74997f2f9b8febf4e8bd3a89d79b80e?rik=lxohVZwQ31rLEA&riu=http%3a%2f%2fww3.sinaimg.cn%2fbmiddle%2fceeb653ejw1fb5e0mjp84j20c80b8wf3.jpg&ehk=CYi2nHcifpXPECm9xxe42TFmbly7u4n3%2bvt9eT%2bIXXg%3d&risl=&pid=ImgRaw&r=0&sres=1&sresct=1', tag: 'grief' },
                { url: 'https://pic1.zhimg.com/v2-33972e894d48c292918666ea4dc4a273_r.jpg?source=1940ef5c', tag: 'laughter' },
                { url: 'https://tse4-mm.cn.bing.net/th/id/OIP-C.-OmEEQPrTFoG-Z4uqizMAgHaHA?pid=ImgDet&rs=1', tag: 'laughter' },
                { url: 'https://pic3.zhimg.com/v2-7eaed7761fb4be639b39b238867c619b_r.jpg?source=1940ef5c', tag: 'laughter' },
                { url: 'https://pic3.zhimg.com/v2-b5f466d96460f330f478c5225bf64c06_r.jpg?source=1940ef5c', tag: 'laughter' },
                { url: 'https://ts1.cn.mm.bing.net/th/id/R-C.76535f10babbed52b7221b7c9de20243?rik=MD5mpqPzglqCIA&riu=http%3a%2f%2fwx4.sinaimg.cn%2fbmiddle%2fceeb653ely8gqg2zw9jgvj205s066mx3.jpg&ehk=ST1Yh25Siqandyu2cNlYatJQVblsZwvkq0bbASue8FY%3d&risl=&pid=ImgRaw&r=0&sres=1&sresct=1', tag: 'shock' },
                { url: 'https://tse1-mm.cn.bing.net/th/id/OIP-C.ei6TZLq8qYE6pWkFAhdHKgHaHa?pid=ImgDet&rs=1', tag: 'shock' },
                { url: 'https://ts1.cn.mm.bing.net/th/id/R-C.7e7b2128f000ad85251aff594276b730?rik=ogIL8Ud2wVURVw&riu=http%3a%2f%2fwx1.sinaimg.cn%2flarge%2f006ARE9vgy1g2jgzljsh3j30k00k0js4.jpg&ehk=oX3vjy7wLC%2fqWaOTW9wypYf14C6ieJkDhDy06o5RtYo%3d&risl=&pid=ImgRaw&r=0', tag: 'shock' },
                { url: 'https://static.fotor.com.cn/assets/stickers/freelancer_lmf_1121_02/8b537166-806a-43cf-a06f-457f6ba1ff32_medium_thumb.jpg', tag: 'shock' },
                { url: 'https://ts1.cn.mm.bing.net/th/id/R-C.573308dbee95bce4e99ac75f95998183?rik=zGPUGP7EAp2%2fEQ&riu=http%3a%2f%2fhimg2.huanqiu.com%2fattachment2010%2f2018%2f0813%2f20180813120424689.jpg&ehk=fWrly1RxZsPVhLIX6vQtldzMEg3JEpH4FJszbV5phTc%3d&risl=&pid=ImgRaw&r=0', tag: 'silence' },
                { url: 'https://ts1.cn.mm.bing.net/th/id/R-C.5fe860cba29f838d4195e337d5f10a42?rik=ffyxuYQb9lpZhg&riu=http%3a%2f%2fwx3.sinaimg.cn%2fbmiddle%2f006qOO1Xly1gt546id2txj309q09qjri.jpg&ehk=FBKmimRmFJy%2bw1hb0pqA3NF7rnRqP7kQSjzo0jC%2bkek%3d&risl=&pid=ImgRaw&r=0', tag: 'silence' },
                { url: 'https://pic4.zhimg.com/v2-832343bf940966d74cc86c05808f9fd1_r.jpg?source=1940ef5c', tag: 'silence' },
                { url: 'https://tse4-mm.cn.bing.net/th/id/OIP-C.D1sM-k3T5nk3Pzm7K7ZV1AHaHa?pid=ImgDet&rs=1', tag: 'silence' },
                { url: 'https://pic3.zhimg.com/50/v2-c351862b3be4c0c32b2e91d85bc5ba47_720w.jpg?source=1940ef5c', tag: 'joker' },
                { url: 'https://ts1.cn.mm.bing.net/th/id/R-C.bda4a4835dfd24280d9228dd8ff44fbb?rik=OQUDj%2bSwm9Ohww&riu=http%3a%2f%2fwx4.sinaimg.cn%2flarge%2f0068Lfdely1gm8aw4h926j30iq0iq7a3.jpg&ehk=0UN%2bnZt6UDiDuc3x1L1XuHxEB84bdFpOSt%2f0CaX82sg%3d&risl=&pid=ImgRaw&r=0', tag: 'joker' },
                { url: 'https://pic2.zhimg.com/50/v2-f9c387435b554b7229f0796b575826a9_hd.jpg?source=1940ef5c', tag: 'joker' },

            ],
            columns: [0, 0, 0, 0],
            selectedSubSubOption: null,
            imagesLoaded: 0,
            markedImages: [],
        };
    },
    mounted() {
        this.intervalId = setInterval(() => {
            if (this.activeIndex === '2' && this.isLoggedIn === true) {
                this.arrangeImages('.content', this.markedImages);
            }
        }, 1);

        this.arrangeImages();
        window.addEventListener('resize', this.arrangeImages);
        this.$nextTick(() => {

            const customRadioGroupHeight = this.$refs.customRadioGroup ? this.$refs.customRadioGroup.offsetHeight : 0;
            const subOptionsHeight = this.$refs.subOptions ? this.$refs.subOptions.offsetHeight : 0;
            const subSubOptionsHeight = this.$refs.subSubOptions ? this.$refs.subSubOptions.offsetHeight : 0;


            const totalHeight = customRadioGroupHeight + subOptionsHeight + subSubOptionsHeight;


            if (this.$refs.container) {
                this.$refs.container.style.marginTop = `${totalHeight}px`;
            }
        });
    },
    beforeDestroy() {
        window.removeEventListener('resize', this.arrangeImages);
        clearInterval(this.intervalId);
    },
    activated() {
        this.arrangeImages();
    },
    methods: {
        checkImagesLoaded() {
            const interval = setInterval(() => {
                const allImages = Array.from(document.querySelectorAll('.image'));
                const allLoaded = allImages.every(img => img.complete);

                if (allLoaded) {
                    clearInterval(interval);
                    this.$nextTick(() => {
                        this.arrangeImages();
                    });
                }
            }, 100);
        },
        toggleMark(image) {
            const index = this.markedImages.findIndex(item => item.url === image.url);
            if (index === -1) {
                this.markedImages.push(image);
            } else {
                this.markedImages.splice(index, 1);
            }
        },
        isMarked(image) {
            return this.markedImages.some(item => item.url === image.url);
        },
        handleSelect(key) {
            this.activeIndex = key;
        },
        changeSelectedMainOption(option) {
            if (this.selectedMainOption === option) {
                this.toggleSubOptions(true);
            } else {
                this.selectedMainOption = option;
                this.showSubOptions = true;
                this.selectedSubOption = '';
                this.updateSubOptions();
            }
            this.subSubOptions = [];
            this.selectedSubSubOption = '';

        },
        toggleSubOptions(clearMain = false) {
            this.showSubOptions = !this.showSubOptions;
            if (clearMain) {
                this.selectedMainOption = '';
                this.selectedSubOption = '';
            }
        },
        updateSubOptions() {
            if (this.selectedMainOption === 'option1') {
                this.subOptions = ['cartoon', 'anime'];
            } else if (this.selectedMainOption === 'option2') {
                this.subOptions = ['Baidu Post Bar Selection', 'Abstract character'];
            } else if (this.selectedMainOption === 'option3') {
                this.subOptions = ['Anger', 'grief', 'laughter', 'shock', 'silence', 'joker'];
            }
        },
        changeSelectedSubOption(option) {
            if (this.selectedSubOption === option) {
                this.selectedSubOption = '';
                this.subSubOptions = [];
            } else {
                this.selectedSubOption = option;
                this.selectedSubSubOption = '';
                this.updateSubSubOptions();
            }
        },

        updateSubSubOptions() {
            if (this.selectedSubOption === 'cartoon') {
                this.subSubOptions = ['Doraemon', 'pokemon', 'Crayon Shin Chan'];
            } else if (this.selectedSubOption === 'anime') {
                this.subSubOptions = ['Bocchi the Rock', 'Ania', 'POPPY PAPPY DAY'];
            } else if (this.selectedSubOption === 'Baidu Post Bar Selection') {
                this.subSubOptions = ['melon-eating masses', 'Attack Post Owner'];
            } else if (this.selectedSubOption === 'Abstract character') {
                this.subSubOptions = ['Sun Xiaochuan', 'Tenzing Tsondu', 'Wang Jingze'];
            } else {
                this.subSubOptions = [];
            }
        },
        changeSelectedSubSubOption(option) {
            if (this.selectedSubSubOption === option) {
                this.selectedSubSubOption = '';
            } else {
                this.selectedSubSubOption = option;
            }
        },
        login(username, password) {
            if (username === 'admin' && password === '123456') {
                this.isLoggedIn = true;
                this.loginError = '';
            } else {
                this.isLoggedIn = false;
                this.loginError = 'Wrong username or password';
            }
        },
        logout() {
            this.isLoggedIn = false;
        },
        editProfile() {
            this.editable = true;
            this.accountCopy = JSON.parse(JSON.stringify(this.account));
        },
        saveProfile() {
            this.editable = false;

        },
        cancelEdit() {
            this.editable = false;
            this.account = JSON.parse(JSON.stringify(this.accountCopy));
        },
        goToLogin() {
            this.dialogVisible = false;
            this.activeIndex = '3';
        },
        onImageLoad() {
            this.imagesLoaded++;
            const totalImages = this.activeIndex === '2' ? this.favoriteImages.length : this.filteredImages.length;
            if (this.imagesLoaded === totalImages) {
                this.$nextTick(() => {
                    this.arrangeImages();
                });
            }
        },
        arrangeImages() {
            const containerWidth = document.querySelector('.content').offsetWidth;
            const columnWidth = containerWidth / 4;
            const columns = [0, 0, 0, 0];
            const containers = Array.from(document.querySelectorAll('.image-container'));
            containers.forEach((container, index) => {
                const minHeight = Math.min(...columns);
                const minIndex = columns.indexOf(minHeight);
                container.style.position = 'absolute';
                container.style.top = `${minHeight}px`;
                container.style.left = `${minIndex * columnWidth}px`;
                columns[minIndex] += container.offsetHeight + 20;
                const maxHeight = Math.max(...columns);
                this.$refs.container.style.height = `${maxHeight}px`;
            });
        }
    },
    watch: {

        activeIndex(newVal, oldVal) {
            if (newVal === '1') {
                this.$nextTick(() => {
                    this.arrangeImages();
                });
            }
            if (newVal === '2' && !this.isLoggedIn) {
                this.dialogVisible = true;
            }
        },
        selectedMainOption() {
            this.$nextTick(() => {
                this.arrangeImages();
            });
        },
        selectedSubOption() {
            this.$nextTick(() => {
                this.arrangeImages();
            });
        },
        selectedSubSubOption() {
            this.$nextTick(() => {
                this.arrangeImages();
            });
        },
        changeSelectedMainOption() {
            this.$nextTick(() => {
                this.arrangeImages();
            });
        },
        changeSelectedSubOption() {
            this.$nextTick(() => {
                this.arrangeImages();
            });
        },
        changeSelectedSubSubOption() {
            this.$nextTick(() => {
                this.arrangeImages();
            });
        },
        filteredImages() {
            this.$nextTick(() => {
                this.arrangeImages();
            });
        },
    },
    computed: {
        filteredImages() {
            let filtered = this.images;
            let allowedTags = [];
            if (this.selectedMainOption) {
                switch (this.selectedMainOption) {
                    case 'option1':
                        allowedTags = ['Doraemon', 'pokemon', 'Crayon Shin Chan'];
                        break;
                    case 'option2':
                        allowedTags = ['melon-eating masses', 'Attack Post Owner', 'Sun Xiaochuan', 'Tenzing Tsondu', 'Wang Jingze'];
                        break;
                    case 'option3':
                        allowedTags = ['Anger', 'grief', 'laughter', 'shock', 'silence', 'joker'];
                        break;
                    default:
                        break;
                }
            }
            if (this.selectedSubOption) {
                switch (this.selectedSubOption) {
                    case 'cartoon':
                        allowedTags = ['Doraemon', 'pokemon', 'Crayon Shin Chan'];
                        break;
                    case 'anime':
                        allowedTags = ['Bocchi the Rock', 'Ania', 'POPPY PAPPY DAY'];
                        break;
                    case 'Baidu Post Bar Selection':
                        allowedTags = ['melon-eating masses', 'Attack Post Owner'];
                        break;
                    case 'Abstract character':
                        allowedTags = ['Sun Xiaochuan', 'Tenzing Tsondu', 'Wang Jingze'];
                        break;
                    case 'Anger':
                        allowedTags = ['Anger'];
                        break;
                    case 'grief':
                        allowedTags = ['grief'];
                        break;
                    case 'laughter':
                        allowedTags = ['laughter'];
                        break;
                    case 'shock':
                        allowedTags = ['shock'];
                        break;
                    case 'silence':
                        allowedTags = ['silence'];
                        break;
                    case 'joker':
                        allowedTags = ['joker'];
                        break;

                    default:
                        break;
                }
            }

            if (this.selectedSubSubOption) {
                allowedTags = [this.selectedSubSubOption];
            }

            if (allowedTags.length > 0) {
                filtered = filtered.filter(image => allowedTags.includes(image.tag));
            }

            return filtered;
        },
    },


};


</script>


<style scoped>
.nav-bar {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 1000;
}

.content {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    position: relative;

}

.sub-options {
    margin-top: 20px;
}

.slide-enter-active,
.slide-leave-active {
    transition: all 0.5s ease;
    transition-property: opacity, transform;
}

.slide-enter,
.slide-leave-to {
    transform: translateY(-100%);
    opacity: 0;
}

.custom-radio-group {
    display: flex;
    width: 100%;
}

.custom-radio-item {
    flex-grow: 1;
    text-align: center;
    padding: 10px;
    cursor: pointer;
    z-index: 3;
}

.custom-radio-item.active {
    background-color: #f2f2f2;
}

.sub-options {
    margin: 0;
    display: flex;
    width: 100%;
    z-index: 2;
}

.subSub-options {
    margin: 0;
    display: flex;
    width: 100%;
    z-index: 1;
}

.custom-radio-item {
    flex-grow: 1;
    text-align: center;
    padding: 10px;
    cursor: pointer;
    background-color: #ff94a6;
}

.custom-radio-item.active {
    background-color: #ff90c8;

}


.sub-options .custom-radio-item {
    background-color: #ffc7da80;

}


.sub-options .custom-radio-item.active {
    background-color: #ffaffc80;

}

.subSub-options .custom-radio-item {
    background-color: #ffc7da80;

}

.subSub-options .custom-radio-item.active {
    background-color: #ffaffc80;

}

.error {
    color: red;
}

.image-container {
    box-sizing: border-box;
    border: 1px solid #ccc;
    border-radius: 5px;
    padding: 10px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
    width: calc(25% - 10px);
    margin-top: 50px;

    position: absolute;
}

#image-grid {
    position: relative;
}

.image {
    width: 100%;
    height: auto;
}

.marked {
    background-color: rgba(255, 0, 0, 0.5);

}
</style>
