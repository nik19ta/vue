<template>
<div class="profile">
  <div id="info" class='addTeam'>
    <h1>Редактировать данные</h1>
    <div class="">
      <p>Фамилия, Имя, Очество</p>
      <input id="fio" type="text" name="team_name" placeholder="FIO" :value="inform.fio">
      <p>Дата Рождения</p>
      <input id='dataof' type="text" name="" placeholder="19-07-2000" :value="inform.date_of_birth">
      <p>Город</p>
      <input id='city' type="text" name="" placeholder="Moskow" :value="inform.city">
      <button type="button" @click='changedata' placeholder='письмо' name="button">Сохранить изменения</button>
    </div>
  </div>
  <div id="addacc" class='addacc'>
    <div class="">
      <h2 class='virttext'>Добавить аккаунт Виртономики</h2>
      <input class='virtinp' :value='inform.account' name='accadd' type="text" placeholder="Например 1370892">
      <button class='virtbtn' type="button" @click='addacc' placeholder='письмо' name="button">Добавить аккаунт </button>
    </div>
  </div>
  <div id="pass" class='addTeam'>
    <h1>Изменения пароля</h1>
    <div class="">
      <p>Старый пароль</p>
      <input id="passw" type="text" name="team_name" placeholder="Введите старый пароль" value="">
      <p>Новый пароль</p>
      <input id='passw1' type="text" name="" placeholder="Придумайте новый пароль" value="">
      <p>Подтверждение нового пароля</p>
      <input id='passw2' type="text" name="" placeholder="Подтвердите новый пароль" value="">
      <button type="button" @click='changepassword' placeholder='письмо' name="button">Сохранить изменения</button>
    </div>
  </div>
  <div class='title-name'>
    <p>Профиль</p>
  </div>
  <div class="content-profile">
    <div class="content-profile-child">
      <div class="content-profile-all">
        <img @click='menu()' class='content-profile-image' :src="inform.img" alt="Avatar">
        <p class='chenge-photo' style="">Изминить фотографию</p>
      </div>
      <div class="about">
        <div class='online'>{{online}}</div>
        <div class='fio'>{{inform.fio}}</div>
        <div class="status">{{inform.login}}</div>
        <div class="media-all">
          <div class="content">
            <div class='names'>
              <p>Электронная почта:</p>
              <p>День рожденья:</p>
              <p>Пол:</p>
              <p>Город:</p>
            </div>
            <div class='params'>
              <p>{{inform.email}}</p>
              <p>{{inform.date_of_birth}}</p>
              <p>{{inform.gender}}</p>
              <p>{{inform.city}}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="link-mid">
      <p @click='rddata'>Редактировать данные</p>
      <p @click='ChangePass'>Изменить пароль</p>
      <p v-if='inform.account' @click='addaccvirtomonica'>Изменить аккаунт Виртономики</p>
      <p v-else @click='addaccvirtomonica'>Добавить аккаунт Виртономики</p>
    </div>
  </div>
  <div class="rating">
    <h1> Личный рейтинг</h1>
  </div>
  <hr class="line">
  <div class="scroll">

    <div v-if='inform.account > 1' v-for='virt in virtomonika' class="blockR">
      <div :id='virt.template_id' class="more-n">
        <h4>{{virt.name}}</h4>
        <p>Описание: {{virt.descr}}</p>
        <p>Категория: {{virt.category_name}}</p>
        <p>Счет: {{virt.score}}</p>
        <p>Уровень: {{virt.level}}</p>
        <p>Дата: {{virt.reached}}</p>
      </div>
      <p class="bloclR-text-title">{{virt.name}}</p>
      <p class="bloclR-text">
        <img class='image' src="../assets/beaker.png" alt="">
        <span class='text-span'>
          Вы достигли {{virt.level}} уровня!
        </span>
      </p>
      <div class="status-bar">
        <div class='points'>
          <p>{{Math.round(virt.score)}} очков </p>
          <p>{{Math.round(virt.progress)}}%</p>
        </div>
        <progress :value="virt.progress" max="100"></progress>
      </div>
      <div class="next">
        <a href="#">Перейти к игре ➔</a>
        <a @click='moreinfo(virt.template_id)' class='moreinfo'>•••</a>
      </div>
    </div>

  </div>
  <!-- <div class="rating">
    <h1> Хардскилы </h1>
    <hr class="line">
    <div class="scroll2">
      <div v-for='hards  in virtonomika_hard' class=" hards">
        <div :id='hards.kind' class='hards_col' v-bind:style="{ background: 'rgb(252,88,48)', height: (hards.value/0.2) + '5px' }">
          <span class='value'>{{hards.value}}</span>
        </div>
        <span class='kind'>{{hards.kind}}</span>
      </div>
    </div>
  </div> -->



  <div class="rating">
    <h1> Хардскилы </h1>
    <hr class="line">
    <div class="scroll2">
      <div v-if='inform.account > 1' style="width: calc(100%/5)" v-for='i  in 5' class=" hards">
        <div :id='virtonomika_hard[i].kind' class='hards_col' v-bind:style="{ background: 'rgb(252,88,48)', height: (virtonomika_hard[i].value/0.2 + i) + '5px' }">
          <span class='value'>{{virtonomika_hard[i].value +i}}</span>
        </div>
        <span class='kind'>{{dict[i].name}}</span>
      </div>

    </div>
  </div>



  <div class="rating">
    <h1> Софтскилы </h1>
    <hr class="line">
    <div class="scroll2">
      <div v-if='inform.account > 1' style="width: calc(100%/5)" v-for='i  in 5' class=" hards">
        <div :id='virtonomika_hard[i +5].kind' class='hards_col' v-bind:style="{ background: 'rgb(252,88,48)',
        height: (virtonomika_hard[i+5].value/0.2 + i) + '5px' }">
          <span class='value'>{{virtonomika_hard[i+5].value+i}}</span>
        </div>
        <span class='kind'>{{dict[i +5].name}}</span>
      </div>

    </div>
  </div>


  <!-- <div class="rating">
    <h1> Софтскилы </h1>
    <hr class="line">
    <div class="scroll2">
    </div>
  </div> -->
</div>
</template>

<script>
import $ from "jquery"
export default {
  name: 'prof',
  props: {
    inform: {},
    virtomonika: {},
    virtonomika_hard: {},
    dict: {}
  },
  data() {
    return {
      online: 'В сети',
    }
  },
  mounted() {
    if (this.inform != '') {
      this.$emit('ajax', '')
      this.$emit('hardskills', '')
      this.$emit('dictfunc', '')
    }
  },
  methods: {
    rddata() {
      $("#info").addClass("addTeamVisible");
      $(document).mouseup(function(e) {
        var info = $("#info");
        if (!info.is(e.target) && info.has(e.target).length === 0) {
          $("#info").removeClass("addTeamVisible")
        } else {}
      });
    },
    changedata() {
      let lthis = this;
      $.ajax({
        type: "POST",
        url: "http://91.201.54.125:5000/chengedata",
        CrossDomain: true,
        data: {
          id: lthis.inform.id,
          login: lthis.inform.login,
          fio: $('#fio').val(),
          dataof: $('#dataof').val(),
          city: $('#city').val(),
        },
        success: function() {}
      });
      $("#info").removeClass("addTeamVisible")
      this.inform.fio = $('#fio').val()
      this.inform.date_of_birth = $('#dataof').val()
      this.inform.city = $('#city').val()
    },
    moreinfo(data) {
      $("#" + data).addClass("more")
      $(document).mouseup(function(e) {
        var pass = $("#" + data);
        if (!pass.is(e.target) && pass.has(e.target).length === 0) {
          $("#" + data).removeClass("more")
        } else {}
      });
    },
    changepassword() {
      $.ajax({
        type: "POST",
        url: "http://91.201.54.125:5000/changepass",
        CrossDomain: true,
        async: false,
        data: {
          passw: $('#passw').val(),
          passw2: $('#passw1').val(),
          id: this.inform.id
        },
        success: function() {}
      });
    },
    ChangePass() {
      $("#pass").addClass("addTeamVisible");
      $(document).mouseup(function(e) {
        var pass = $("#pass");
        if (!pass.is(e.target) && pass.has(e.target).length === 0) {
          $("#pass").removeClass("addTeamVisible")
        } else {}
      });
    },
    addacc() {
      let lthis = this;
      if (document.getElementsByName('accadd')[0].value) {
        $.ajax({
          type: "POST",
          url: "http://91.201.54.125:5000/addacc",
          CrossDomain: true,
          data: {
            accid: document.getElementsByName('accadd')[0].value,
            id: this.inform.id,
            login: this.inform.login,
            password: this.inform.password,
          },
          success: function(data) {
            alert(data)
            lthis.inform.account = document.getElementsByName('accadd')[0].value;
            lthis.ajax()
          }
        });
      }
    },
    addaccvirtomonica() {
      $("#addacc").addClass("addTeamVisible");
      $(document).mouseup(function(e) {
        var pass = $("#addacc");
        if (!pass.is(e.target) && pass.has(e.target).length === 0) {
          $("#addacc").removeClass("addTeamVisible")
        } else {}
      });
    }
  }
};
</script>

<style scoped>
#management {
  background: rgba(97, 125, 138) !important;
}

#trade {
  background: rgba(252, 88, 48) !important;
}

#manufacture {
  background: rgba(144, 195, 69) !important;
}

#mining {
  background: rgba(21, 149, 136) !important;
}

#research {
  background: rgba(30, 170, 241) !important;
}

#advert {
  background: rgba(64, 84, 178) !important;
}

#farming {
  background: rgba(230, 61, 99) !important;
}

#animal {
  background: rgba(79, 172, 83) !important;
}

#fishing {
  background: rgba(192, 208, 70) !important;
}

#service {
  background: rgba(97, 125, 138) !important;
}

#medicine {
  background: #4a2377 !important;
}

#restaurant {
  background: rgba(144, 195, 69) !important;
}

#power {
  background: rgba(21, 149, 136) !important;
}

#car {
  background: rgba(30, 170, 241) !important;
}

#it {
  background: rgba(64, 84, 178) !important;
}

#educational {
  background: rgba(230, 61, 99) !important;
}

.more-n {
  opacity: 0;
  visibility: hidden;
  position: absolute;
  top: -30px;
  width: 400px;
  min-height: 270px;
  box-shadow: 0px 0px 15px 1px #0001;
  padding: 10px;
  background: #fff;
  border-radius: 3px;
  left: calc(50vw - 200px);
  transition: all 0.3s;
}

.more {
  visibility: visible;
  opacity: 1;
  top: calc(50vh - 135px);
  z-index: 9;
}

.image {
  position: absolute;
  margin-top: -2px;
}

.moreinfo {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 17px;
}

.text-span {
  margin-left: 25px;
}

.virtinp {
  border-radius: 10px;
  border: none;
  width: 100%;
  height: 55px;
  background: #eeeff2;
  padding-left: 20px;
  font-size: 16px;
  margin-top: 10px;
}

.virttext {
  margin-top: 30px;
}

.virtbtn {
  margin-top: 40px;
  border-radius: 10px;
  border: none;
  width: 100%;
  height: 60px;
  background: #ff7f00;
  font-size: 18px;
  color: #fff;
  -webkit-box-shadow: 0px 0px 20px 0px #0003;
  box-shadow: 0px 0px 20px 0px #0003;
}

.scroll {
  display: flex;
  justify-content: flex-start;
  flex-wrap: wrap;
  width: calc(100% + 24px);
  padding-bottom: 50px;
  margin: -5px -12px;
}

.scroll2 {
  display: flex;
  justify-content: space-around;
  align-items: flex-end;
  flex-wrap: nowrap;
  width: calc(100%);
  padding-bottom: 50px;
}

.hards {
  width: calc(100%/16);
  margin-top: 10px;
  bottom: 0;
  text-align: center;
}

.hards_col {
  width: 100%;
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 15px;
}

.kind {
  display: block;
  margin-top: 10px;
  height: 00px;
  font-size: 12.5px;
  white-space: pre-wrap;
}

.rating_flex p {
  padding: 20px;
  text-align: center;
  width: 100%;
  border-radius: 5px;
  font-size: 20px;
}

.addacc {
  width: 550px;
  height: 270px;
  position: absolute;
  top: 50%;
  left: 50%;
  z-index: 9;
  margin: -155px 0 0 -275px;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0px 0px 30px 0px #0003;
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  justify-content: center;
  align-items: flex-start;
  visibility: hidden;
  opacity: 0;
  transition: all 0.4s;
  transform: translateY(-850px);
}

.addTeam {
  width: 550px;
  height: 560px;
  position: absolute;
  top: 50%;
  left: 50%;
  z-index: 9;
  margin: -305px 0 0 -275px;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0px 0px 30px 0px #0003;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: flex-start;
  visibility: hidden;
  opacity: 0;
  transition: all 0.4s;
  transform: translateY(-850px);
}

.addTeamVisible {
  visibility: visible;
  opacity: 1;
  transform: translateY(0px);
}

.addTeamVisibleDown {
  visibility: visible;
  opacity: 1;
  transform: translateY(300px);
  visibility: hidden;
  opacity: 0;
}

.addTeam h1 {
  text-align: center;
  margin-top: -20px;
  transform: translateY(50px);
}

.addTeam div {
  width: 80%;
}

.addTeam div p {
  color: #7f7f7f;
  font-size: 15px;
}

.addTeam input {
  border-radius: 10px;
  border: none;
  width: 100%;
  height: 55px;
  background: #eeeff2;
  padding-left: 20px;
  font-size: 16px;
}

.addTeam textarea {
  border-radius: 10px;
  border: none;
  width: 100%;
  height: 200px;
  background: #eeeff2;
  padding-left: 20px;
  padding-top: 20px;
  font-size: 16px;
}

.addTeam button {
  margin-top: 40px;
  border-radius: 10px;
  border: none;
  width: 100%;
  height: 60px;
  background: #ff7f00;
  font-size: 18px;
  color: #fff;
  box-shadow: 0px 0px 20px 0px #0003;
}

.profile {
  width: 1200px;
  font-style: normal;
  padding-top: 60px;
  padding-bottom: 60px;
}

.line {
  width: 100%;
  color: #c4cbd1;
}

.status {
  font-size: 108%;
  font-weight: 500;
  color: #4d5d66;
}

.params {
  font-weight: 600;
  color: #363738;
  font-size: 15px;
}

.fio {
  font-weight: 600;
  font-size: 20px;
  margin-bottom: 5px;
  width: auto;
  color: #060606;
}

.content {
  display: flex;
  justify-content: space-between;
  width: 92%;
  font-size: 15px;
}

.names {
  color: #8f8f91;
}

.title-data {
  text-align: center;
  font-size: 28px;
  font-weight: bold;
}

.placeholder {
  color: #7f7f7f;
  font-size: 15px;
  text-align: left;
}

.form-data {
  width: 75%;
  height: 90%;
}

.form-data input {
  background: #eeeff2;
  padding-left: 20px;
  width: 95%;
  height: 50px;
  border-radius: 6px;
  border: 0px solid;
  font-size: 20px;
  margin-top: -10px;
}

.form-data button {
  background: #ff7f00;
  width: 100%;
  height: 50px;
  border-radius: 6px;
  border: 0px solid;
  color: #fff;
  font-size: 18px;
  font-weight: 500;
  box-shadow: 0px 0px 20px 0px #0004;
  margin-top: 25px;
}

.inp {
  font-size: 16px !important;
}

.chenge-photo {
  text-align: center;
  width: 100%;
  color: #ff8000;
  cursor: pointer;
}

.online {
  background: #67fe3e;
  border-radius: 5px;
  width: 60px;
  padding-left: 10px;
  padding-top: 3px;
  padding-bottom: 3px;
  margin-bottom: 10px;
  font-size: 14px;
  font-weight: 500;
}

.content-profile-child {
  display: flex;
  justify-content: space-around;
  align-items: flex-start;
  width: 650px;
}

.rating_flex {
  display: flex;
  justify-content: flex-start;
  flex-wrap: wrap;
  width: 100%;
}

.font_bold {
  color: #000;
  padding-left: 15%;
  font-size: 15px;
  font-weight: Regular;
  width: 180px;
}

.about {
  width: 400px;
  height: 100%;
}

.title-name {
  text-align: left;
  font-weight: 700;
  font-size: 35px;
  color: #313132;
  width: 100%;
}

.content-profile-all {
  height: 100%;
  margin-right: 30px;
}

.content-profile {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  flex-wrap: wrap;
}

.content-profile-image {
  width: 250px;
  height: 250px;
  border-radius: 10px;
  border: 0 solid;
}

.link-mid {
  width: 20% !important;
  text-align: right;
  width: 100%;
  color: #ff8000;
  font-size: 15px;
  font-weight: 500;
  margin-top: -20px;
  cursor: pointer;
}

.rating {
  font-size: 10px;
  font-weight: 400;
  width: 100%;
}

.blockR {
  background-color: #fff;
  width: 31.3%;
  height: auto;
  margin: 1%;
  border-radius: 7px;
}

.points p {
  font-size: 12px;
  height: 0px;
  color: #3b5767;
  margin-top: 0px;
  margin-top: 10px;
}

.points {
  width: 100%;
  padding-left: 20px;
  padding-right: 20px;
  display: flex;
  justify-content: space-between;
}

progress {
  border: 0;
  width: 90%;
  height: 8px;
  border-radius: 100px;
  margin-left: 20px;
  background: #eeeff2;
}

progress::-webkit-progress-value {
  border-radius: 5px;
  background: #ff7f00;
}

progress::-webkit-progress-bar {
  width: 90%;
  border-radius: 5px;
  background: #eeeff2;
}

progress::-moz-progress-bar {
  border-radius: 5px;
  background: #ff7f00;
}

.bloclR-text-title {
  margin-left: 20px;
  font-weight: bold;
}

.bloclR-text {
  margin-left: 20px;
}

.bloclR-text img {
  width: 20px;
}

.status-bar {
  width: 100%;
  height: 50px;
  background-color: #f3f5f6;
  display: flex;
  justify-content: flex-start;
  flex-flow: wrap;
}

.next {
  width: 100%;
  height: 50px;
  padding-right: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.next a {
  margin-left: 20px;
  text-decoration: none;
  color: #a4a5a7;
}

/* 1300 px */
@media screen and (max-width: 1300px) {
  .profile {
    width: 90%;
  }

  /* .addacc {
    width: 550px;
    height: 220px;
    top: 50%;
    left: 50%;
    z-index: 9;
    margin: -155px 0 0 -275px;
    background: #fff;
    border-radius: 10px;
    box-shadow: 0px 0px 30px 0px #0003;
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    justify-content: center;
    align-items: flex-start;
  } */
}

/* 1100 px  */
@media screen and (max-width: 1100px) {
  .blockR {
    width: 48%;
    height: auto;
    margin: 1%;
    border-radius: 10px;
  }

  .rating_flex {
    width: 80%;
  }
}

/* 900px */
@media screen and (max-width: 900px) {
  .blockR {
    background-color: #fff;
    width: 380px;
    height: auto;
    margin: 1%;
    border-radius: 10px;
  }

  .content-profile-child {
    width: 100%;
    padding: 0;
    flex-wrap: wrap;
  }

  .content-profile {
    padding: 0;
  }

  .content-profile-all {
    margin: 0;
  }

  .about {
    width: 400px;
  }

  .link-mid p {
    margin-left: 15px;
    margin-right: 15px;
  }

  .content-profile-child {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 650px;
  }

  .content-profile-child {
    display: flex;
    justify-content: space-around;
    align-items: flex-start;
    width: 100%;
  }

  .fio {
    width: 100%;
  }

  .media-all {
    width: 100%;
    display: flex;
    justify-content: flex-start;
  }

  .content {
    width: 100%;
  }

  .title-name {
    font-weight: 900;
    font-size: 35px;
    color: #000;
    text-align: center;
  }

  .rating {
    text-align: center;
    width: 100%;
  }

  .content-profile {
    justify-content: center;
  }

  .link-mid {
    margin-top: 20px;
    width: 75% !important;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
  }

  .content-profile-all {
    margin-right: 0px;
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
  }

  .profile {
    width: 90%;
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
  }

  .rating_flex {
    width: 100%;
    display: flex;
    justify-content: center;
  }
}

@media screen and (max-width: 880px) {
  .scroll {
    justify-content: center;
  }
}

@media screen and (max-width: 880px) {
  .addTeam {
    top: 15%;
    width: 90%;
    height: 70%;
    left: 5%;
    margin: 0;
    border-radius: 5px;
    box-shadow: 0px 0px 30px 0px #0003;
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    justify-content: center;
    align-items: flex-start;
  }

  .addacc {
    top: 32.5555555%;
    width: 90%;
    height: 35%;
    left: 5%;
    margin: 0;
    border-radius: 5px;
    box-shadow: 0px 0px 30px 0px #0003;
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    justify-content: center;
    align-items: flex-start;
  }

  .virtinp {
    width: 80%;
  }
}
</style>
