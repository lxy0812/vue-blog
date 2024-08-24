<template>
    <div class="logo-body">
        <div class="login-panel">
            <div class="login-logo"></div>
            <div class="login-nav">
                <!-- 使用 Vue 的 class 绑定来动态切换类名 -->
                <div class="login-nav-left" :class="{ navFont: isLeftActive }" @click="toggleLeftNav">登录</div>
                <div class="login-nav-right" :class="{ navFont: !isLeftActive }" @click="toggleRightNav">注册</div>
                <div class="bk" :style="bkStyle">
                </div>
            </div>
            <div class="login-form" v-show="isLoginFormActive">
                <!-- 显示登录表单 -->
                <el-form :model="fromData">
                    <el-form-item label="">
                        <el-input placeholder="请输入用户名"
                                    v-model="fromData.account">
                            <template #prefix>
                                <span class="iconfont icon-account"></span>
                            </template>
                        </el-input>
                    </el-form-item>
                    <el-form-item label="">
                        <el-input placeholder="请输入密码"
                                ref="passwordInput"
                                v-model="fromData.password" 
                                :type="addPassFlag ? 'text' : 'password'"
                                @input="handleInput"
                                @focus="handleFocus"
                                @blur="handleBlur">
                            <template #prefix>
                                <span class="iconfont icon-password"></span>
                            </template>
                            <template #suffix>
                                <span class="password-toggle" @click.stop="togglePasswordVisibility">
                                    <el-icon v-if="addPassFlag"><View /></el-icon>
                                    <el-icon v-else><Hide /></el-icon>
                                </span>
                            </template>
                        </el-input>
                    </el-form-item>
                    <div class="demo-auto-login">
                        <el-form-item label="">
                        <el-checkbox v-model="fromData.rememberMe" 
                                    :label="true">记住我</el-checkbox>
                                    <a href="javascript:;">忘记密码</a>
                        </el-form-item>
                    </div>
                    <el-form-item label="">
                        <el-button type="primary" :style="{width:'100%'}">登录</el-button>
                    </el-form-item>
                </el-form>
            </div>
            <div class="login-form" v-show="isRegisterFormActive">
                <!-- 显示注册表单 -->
                <el-form :model="registerData">
                    <el-form-item label="">
                        <el-input v-model="registerData.username" placeholder="请输入用户名">
                            <template #prefix>
                                <span class="iconfont icon-account"></span>
                            </template>
                        </el-input>
                    </el-form-item>
                    <el-form-item label="">
                        <el-input v-model="registerData.email" placeholder="请输入邮箱">
                            <template #prefix>
                                <span class="iconfont icon-youxiang"></span>
                            </template>
                        </el-input>
                    </el-form-item>
                    <el-form-item label="" class="code">
                        <el-input style="width: 70%;" v-model="registerData.checkCode" placeholder="请输入验证码">
                            <template #prefix>
                                <span class="iconfont icon-yanzhengma"></span>
                            </template>
                        </el-input>
                        <el-button style="margin-left: 5%;" type="primary">获取验证码</el-button>
                    </el-form-item>
                    <el-form :model="registerPassword">
                        <el-form-item label="">
                            <el-input ref="passwordInput1"
                                    v-model="registerData.password" 
                                    :type="addPassFlag1 ? 'text' : 'password'"
                                    @input="handleInputPassword"
                                    @focus="handleFocusPassword"
                                    @blur="handleBlurPassword"
                                    placeholder="至少6位密码">
                                <template #prefix>
                                    <span class="iconfont icon-password"></span>
                                </template>
                                <template #suffix>
                                    <span class="password-toggle" @click.stop="togglePasswordVisibility(1)">
                                        <el-icon v-if="addPassFlag1"><View /></el-icon>
                                        <el-icon v-else><Hide /></el-icon>
                                    </span>
                                </template>
                            </el-input>
                        </el-form-item>
                        <el-form-item label="">
                            <el-input ref="passwordInput2"
                                    :type="addPassFlag2 ? 'text' : 'password'"
                                    @input="handleInputConfirmPassword"
                                    @focus="handleFocusConfirmPassword"
                                    @blur="handleBlurConfirmPassword"
                                    v-model="registerData.confirmPassword" 
                                    placeholder="请确认密码">
                                <template #prefix>
                                    <span class="iconfont icon-password"></span>
                                </template>
                                <template #suffix>
                                    <span class="password-toggle" @click.stop="togglePasswordVisibility(2)">
                                        <el-icon v-if="addPassFlag2"><View /></el-icon>
                                        <el-icon v-else><Hide /></el-icon>
                                    </span>
                                </template>
                            </el-input>
                        </el-form-item>
                    </el-form>
                    <el-form-item label="">
                        <el-button type="primary" :style="{width:'100%'}">注册</el-button>
                    </el-form-item>
                </el-form>
            </div>
        </div>
    </div>
</template>

<script setup>
import { reactive, ref } from "vue"
import { View, Hide } from '@element-plus/icons-vue';
const isLoginFormActive = ref(true);
const isRegisterFormActive = ref(false);

// 定义响应式数据
const fromData = reactive({});
const registerData = reactive({});
// 使用 reactive 来创建响应式的布尔值，默认为 true 表示左侧激活
const isLeftActive = ref(true);
// 定义响应式样式对象
const bkStyle = ref({ transform: 'translateX(0%)' });
const addPassFlag = ref(false) // 控制密码显示与否的标识
const password = ref('') // 密码绑定的值
const passwordInput = ref(null);
// 表单数据
const registerPassword = ref({
  password: '',
  confirmPassword: ''
});
let cursorPosition = 0; // 用于保存光标位置

// 控制密码显示的标识
const addPassFlag1 = ref(false);
const addPassFlag2 = ref(false);

// 切换密码显示状态
const togglePasswordVisibility = () => {
  // 保存当前光标位置
  const inputElement = passwordInput.value?.$el.querySelector('input');
  if (inputElement) {
    cursorPosition = inputElement.selectionStart;
  }
  addPassFlag.value = !addPassFlag.value;

  // 延迟一下，等待输入框切换完成，再恢复光标位置
  setTimeout(() => {
    restoreCursorPosition();
  }, 0);
};

// 恢复光标位置
const restoreCursorPosition = () => {
  const inputElement = passwordInput.value?.$el.querySelector('input');
  if (inputElement) {
    inputElement.setSelectionRange(cursorPosition, cursorPosition);
  }
};

// 输入时保持光标位置
const handleInput = () => {
  // 更新光标位置
  const inputElement = passwordInput.value?.$el.querySelector('input');
  if (inputElement) {
    cursorPosition = inputElement.selectionStart;
  }
};

// 输入框获得焦点时也更新光标位置
const handleFocus = () => {
  const inputElement = passwordInput.value?.$el.querySelector('input');
  if (inputElement) {
    cursorPosition = inputElement.selectionStart;
  }
};

// 输入框失去焦点时也更新光标位置
const handleBlur = () => {
  const inputElement = passwordInput.value?.$el.querySelector('input');
  if (inputElement) {
    cursorPosition = inputElement.selectionStart;
  }
};



// 切换左侧导航激活状态的方法
function toggleLeftNav() {
    if (!isLeftActive.value) {
        isLeftActive.value = true;
        // 当左侧激活时，应用 transform 样式为 'translateX(0%)'
        bkStyle.value = { transform: 'translateX(0%)' };
        isLoginFormActive.value = true;
        isRegisterFormActive.value = false;
    }
}

// 切换右侧导航激活状态的方法
function toggleRightNav() {
    if (isLeftActive.value) {
        isLeftActive.value = false;
        // 当右侧激活时，应用 transform 样式为 'translateX(125%)'
        bkStyle.value = { transform: 'translateX(125%)' };
        isLoginFormActive.value = false;
        isRegisterFormActive.value = true;
    }
}

</script>

<style lang="scss">
.logo-body {
    position: relative;
    width: 100%;
    height: calc(100vh);
    background-size: cover;
    background-position: center;
    background-image: url(../assets/background.jpg);
}

.login-panel {
    position: absolute;
    width: 40%;
    max-width: 520px;
    min-width: 360px;
    background: rgba(255, 255, 255, 0.8);
    padding: 20px;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border-radius: 5px;
    box-shadow: 2px 2px 10px #ddd;
    .login-logo{
        width: 60px;
        height: 60px;
        border-radius: 50%;
        background-size: cover;
        background-position: center;
        background-image: url(../assets/logo.png);
        margin: 0 auto;
        margin-bottom: 10px;
    }
    .login-nav{
        display: flex;
        position: relative;
        width: 100%;
        margin-bottom: 20px;
        .login-nav-left, .login-nav-right{
            width: 50%;
            color: #515a6e;
            text-align: center;
            font-size: 18px;
            cursor: pointer; /* 鼠标悬停时鼠标指针变为指针手势 */
        }
        .navFont{
            color: #000;
        }
        .bk {
            margin-left: 5%;
            border: 1px solid #2d8cf0;
            width: 40%;
            position: absolute;
            bottom: -5px;
            transition: 0.5s;
        }
    }
    .demo-auto-login{
        width: 100%;
        position: relative;
        a {
        position: absolute;
        right: 0;
    }
    }
    .password-toggle {
        cursor: pointer;
    }
}

</style>