<template>
 <div>
     <div v-if="loginfail" id="modal" class="modal-backdrop" style="height: 100% !important; display: none; width: 100% !important;">
        <div class="modal" role="dialog" style="display: block; padding-top: 200px; background-color: rgba(0,0,0,0.25);">
            <div class="modal-dialog" role="document">
                <div class="modal-content" style="padding: 20px; border: none !important; border-radius: 10px;">
                    <div>
                        <h5 class="mdTitle">Lỗi đăng nhập</h5>
                    </div>
                    <div class="mdDes">
                        <p>Tên đăng nhập hoặc mật khẩu không đúng.</p>
                    </div>
                    <div style="text-align: right">
                        <button id="tryAgain" type="button" class="mdBtn">Nhập lại</button>
                    </div>
                </div>
            </div>
        </div>
    </div>  
    <img src="/static/pics/cover-manager.png" style="width: 100%; height: auto;">
   <div class="content blueText center margin" style="margin-top: -50px;">
        <div class="tit">Đăng nhập để tiếp tục</div>
         <form action="" method="POST" v-on:submit.prevent="sendLogin" style="margin-top: 20px;"> 
            <p for="username" class="inputTit" style="margin-left: -160px;">TÊN ĐĂNG NHẬP</p>
            <input id="username" type="text" placeholder="Nhập tên đăng nhập" v-model="formdata.username"  style="width: 330px;">
            <p for="username" class="inputTit" style="margin-left: -205px; margin-top: 20px">MẬT KHẨU</p>
            <input id="password" type="password" placeholder="Nhập mật khẩu" v-model="formdata.password" style="width: 330px; margin-right: 20px; margin-left: 70px;">
            <button class="submitBtn" type="submit"><img src="/static/icons/next.png" ></button>
        </form>
   </div>
    </div>
</template>

<script>
$(document).ready(function() {
  $("#submitBtn").click(function() {
    if ($("#username").val() == "") {
      $("#username").addClass("redBorder");
      $("#username").addClass("errorPH");
      $("#username").attr("placeholder", "Vui lòng nhập tên đăng nhập");
    }
    if ($("#password").val() == "") {
      $("#password").addClass("redBorder");
      $("#password").addClass("errorPH");
      $("#password").attr("placeholder", "Vui lòng nhập mật khẩu");
    } else {
      // $("#signin").submit();
      $("#modal").fadeIn("fast");
    }
  });

  $("#username").focusout(function() {
    if ($("#username").val() == "") {
      $("#username").addClass("redBorder");
      $("#username").addClass("errorPH");
      $("#username").attr("placeholder", "Vui lòng nhập tên đăng nhập");
    }
  });

  $("#password").focusout(function() {
    if ($("#password").val() == "") {
      $("#password").addClass("redBorder");
      $("#password").addClass("errorPH");
      $("#password").attr("placeholder", "Vui lòng nhập mật khẩu");
    }
  });

  $("#username").click(function() {
    $("#username").removeClass("redBorder");
    $("#username").removeClass("errorPH");
    $("#username").attr("placeholder", "");
  });

  $("#password").click(function() {
    $("#password").removeClass("redBorder");
    $("#password").removeClass("errorPH");
    $("#password").attr("placeholder", "");
  });

  $("#tryAgain").click(function() {
    $("#modal").fadeOut("fast");
  });
});

import axios from "axios";
import md5 from "crypto-md5";
import {IPGlobal} from "../main.js";

export default {
  data() {
    return {
      title: "Login",
      formdata: {
        role: "employee"
      },
      auth: "",
      access_token: "",
      refresh_token: "",
      loginfail: true
    };
  },
  methods: {
    sendLogin() {
      var flag=true;
      if ($("#username").val() == "") {
        $("#username").addClass("redBorder");
        $("#username").addClass("errorPH");
        $("#username").attr("placeholder", "Vui lòng nhập tên đăng nhập");
        $("#username").addClass("errorShake");
        setTimeout(function(){$("#username").removeClass("errorShake")},500);
        flag=false;
      }
      if ($("#password").val() == "") {
        $("#password").addClass("redBorder");
        $("#password").addClass("errorPH");
        $("#password").attr("placeholder", "Vui lòng nhập mật khẩu");
        $("#password").addClass("errorShake");
        setTimeout(function(){$("#password").removeClass("errorShake")},500);
        flag=false;
      }
      if (flag == true) {
        var passmd5 = md5($("#password").val());
        this.formdata.password = passmd5;
        axios
          .post(`http://${IPGlobal.IP}:3000/api/employee/login`, this.formdata)
          .then(response => {
            if (response.data.auth) {
              this.auth = response.data.auth;
              this.access_token = response.data.access_token;
              this.refresh_token = response.data.refresh_token;
              this.$localStorage.set(
                "access_token",
                response.data.access_token
              );
              this.$localStorage.set(
                "refresh_token",
                response.data.refresh_token
              );
              this.$router.replace({ name: "Manage" });
            }
            else{
                $("#modal").fadeIn("fast");
            }
          })
          .catch(err => {
            alert(err);
          });
      }
    }
  }
};
</script>