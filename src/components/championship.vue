<template>
<div class="testing">
  <div v-if='notificationparams' class="notification">
    <p class='title-notification'>Уведомление: </p> <br>
    <p class='content-notification'>{{notification}}</p>
  </div>
  <div class="Tests">
    <h1 v-if='show'>Чемпионаты</h1>
    <!-- блок чемпионатов -->
    <div v-if='show' @click='championatsdataajax(block)' v-for="block in championats.all">
      <div id="1" class="blockT">
        <div class="data-title-o">
          <p>{{block[6]}}</p>
        </div>
        <div class="pick">
          <img :src="block[2]" alt="">
        </div>
        <div class="title-text">
          <p>{{block[1]}}</p>
        </div>
        <div class="data-text">
          <p>Дата начала: {{block[3].split('/')[0] +'.'+ block[3].split('/')[1]+'.'+block[3].split('/')[2]}}</p>
        </div>
        <div class="link-text">
          <p><a>Подробнее ➔</a></p>
        </div>
      </div>
    </div>
    <div v-if='!show' class="championshipMore">
      <!--  -->
      <div id="addTeam" class='addTeam'>
        <h1>Добавление команды</h1>
        <div class="">
          <p>Название команды</p>
          <input id="team_name" type="text" name="team_name" value="">
          <p>Номинация</p>
          <input id='team_nomination' type="text" name="team_nomination" value="">
          <p>Описание</p>
          <textarea id='team_discribtion' name="team_discribtion"></textarea>
          <button @click='AddToTeamFunc' type="button" placeholder='описание' name="button">Сохранить команду</button>
        </div>
      </div>
      <!--  -->
      <div id="addUserTeam" class='addTeam'>
        <h1>Пригласить в команду</h1>
        <div class="">
          <p>Название команды</p>
          <input v-model='teamsforinv' readonly type="text" value="">
          <p>Номинация</p>
          <input v-model='teamdescribtion' readonly id='nomination' type="text" value="">
          <p>Сопроводительное письмо</p>
          <textarea id='coverletter'></textarea>
          <button type="button" placeholder='письмо' @click='invitetoteam' name="button">Пригласить в команду</button>
        </div>
      </div>
      <!--  -->
      <div class="bl">
        <p class='title-link'>
          <span @click="championshipFun" class='par'>Чемпионаты</span>
          <span class='partic'> — {{championatinfo[1]}}</span>
        </p>
        <p class='title'>{{championatinfo[1]}}</p>
        <div class="ab-w">
          <div class="ab">
            <p @click='partic=true, teams=false' class='default' :class="[partic ? 'namech' : false]">
              Участники ({{userschampionats.length}})
            </p>
            <p class='default' :class="[teams ? 'teams' : false]" @click='teamzero(),partic=false,teams=true'>
              Команды ({{countteam}})
            </p>
          </div>
        </div>
      </div>
      <!--  -->
      <div v-if='partic' class="participants">
        <!-- {{userschampionats}} -->
        <div v-for='users in userschampionats' class="participant ">
          <router-link class="half" :to="{ name: 'user', params: {id:users.data[0][0]} }">
            <img :src="users.data[0][8]" alt="">
            <div class="participant-text">
              <h3 style="color: #000;">{{users.data[0][1]}}</h3>
              <p>{{users.data[0][4]}}, {{users.data[0][7]}}</p>
            </div>
          </router-link>
          <!-- {{users.inv}}
          {{teamsforinv}} -->
          <p @click='addTeams(users.data[0][0])' v-if='in_array(teamsforinv, users.inv)&& invatetrue && users.team && users.data[0][0] !=inform.id' class='invite'>Пригласить </p>
          <p v-if='!in_array(teamsforinv, users.inv)&& invatetrue && users.team && users.data[0][0] !=inform.id' class='invite'>Приглашение отправлено</p>
        </div>
        <!--  -->
        <button v-if='!addto' class="participant-btn" @click='addteamUser' type="button" name="button">Присоедениться к чемпионату</button>
        <button v-if='addto' class="participant-btn" @click='ExitChampionat' type="button" name="button">Выйти из чемпионата</button>
      </div>
      <!--  -->
      <div v-if='teams' class="temsblock">
        <div class="blocks">
          <div @click='user(teams)' :id='teams[0]' v-for='teams in teamsch.all' class="blockteams item">
            <h4> {{teams[1]}}</h4>
            <p v-if="teams[2]" class='addd'>Участников {{teams[2].split(',').length}}</p>
            <p class='ab'>{{teams[4]}}</p>
          </div>
          <button v-if='addto && !invatetrue' class='addteam' @click='invite' type="button" name="button">Добавить команду</button>
        </div>


        <div v-if='ids' class="blocks1">
          <h3>Список участников команды: {{teamnow[1]}}</h3>
          <div v-for='id of ids' class="blockteamsP"> <img :src="id[0][8]" alt="">
            <div class="all">
              <div class="name">
                <p>{{id[0][1]}}</p>
              </div>
              <div class="fio">
                <p>{{id[0][4]}}, {{id[0][2]}}</p>
              </div>
            </div>
          </div>
          <div @click='leavefunc' v-if='leave == true' class="leave">
            <p>Покинуть команду</p>
          </div>
        </div>

      </div>
    </div>
  </div>
</div>
</template>

<script>
import $ from "jquery"

// var idblock;

export default {
  name: 'testing',
  props: {
    championats: {},
    inform: {},
    // все приглашения
    invatedata: {}
  },
  data() {
    return {
      // переключение с карточек чемпионатов на них
      show: true,
      // (Две) ствечает за ораньжевую получу под участниками или командами
      partic: true,
      teams: false,
      // говорит отображать кнопку выйти из чемпионата или присоединится
      addto: false,
      // можно ли покунить команду
      leave: false,
      // Можно ли пригласить человека
      invatetrue: false,
      ids: "",
      // id пользователя которого нужно пригласить в команду
      iduser: '',
      // все команды
      teamsch: '',
      // данные выбранной команды
      teamnow: '',
      // счётчек команд
      countteam: '',
      //имя команды человека если он капитан
      teamsforinv: '',
      //описание команды человека если он капитан
      teamdescribtion: '',
      // id команды человека если он капитан
      teamid: '',
      // информация о чемпионате
      championatinfo: '',
      // все люди в данном чемпионате
      userschampionats: '',
      // если у челвека команда
      teamis: false,
      users: [],
      // уведомление
      notification: '',
      notificationparams: false,
      // приглашения
      invates: ''
    }
  },
  mounted() {
    this.championatMethod()
  },
  methods: {
    notificationfunc(data) {
      let lthis = this;
      setTimeout(function() {
        lthis.notificationparams = true;
        lthis.notification = data;

      }, 0)
      setTimeout(function() {
        lthis.notificationparams = false;
        lthis.notification = '';

      }, 3000)

    },
    teamsinfo() {
      this.teamsforinv = ''
      this.teamid = false
      this.teamis = false

      if (typeof(this.teamsch.all) != "undefined") {}
      let usr = [];
      let user = [];

      for (var i = 0; i < this.teamsch.all.length; i++) {
        // смотрим капитанов и сравниваем
        if (this.teamsch.all[i][6] == this.inform.id) {
          this.invatetrue = true;
          // название команды
          this.teamsforinv = this.teamsch.all[i][1];
          // // // console.log(this.teamsch.all[i]);
          this.teamdescribtion = this.teamsch.all[i][4]
          // id команды человека
          this.teamid = this.teamsch.all[i][0];
          // если у человека есть команда
          this.teamis = true
        }
      }
    },
    teamzero() {
      this.ids = false;
    },
    async leavefunc() {
      let lthis = this;
      let arruser = lthis.teamnow[2].split(',')

      $.ajax({
        type: "POST",
        url: "http://91.201.54.125:5000/deluser",
        CrossDomain: true,
        async: false,
        data: {
          userid: this.inform.id,
          teamid: this.teamnow[1],
        },
        success: function(data) {

          for (var i = 0; i < arruser.length; i++) {
            if (arruser[i] == lthis.inform.id) {
              arruser = arruser.splice(0, i);
              lthis.teamnow[2] = arruser.join();
              lthis.user(lthis.teamnow)

              break;
            }
          }

          lthis.leave = false;
        },
        error: function(error) {}
      });
    },
    addTeams(data) {
      this.iduser = data;
      $("#addUserTeam").addClass("addTeamVisible");
      $(document).mouseup(function(e) {
        var addUserTeam = $("#addUserTeam");
        if (!addUserTeam.is(e.target) && addUserTeam.has(e.target).length === 0) {
          $("#addUserTeam").removeClass("addTeamVisible");
        } else {}
      });
    },
    invite() {
      $("#addTeam").addClass("addTeamVisible");
      $(document).mouseup(function(e) {
        var addTeam = $("#addTeam");

        if (!addTeam.is(e.target) && addTeam.has(e.target).length === 0) {
          $("#addTeam").removeClass("addTeamVisible");
        } else {}
      });
    },
    async user(data) {
      // console.log(data);
      $(".item").css({
        background: "#fff",
        color: "#000"
      });
      $("#" + data).css({
        background: "#ff7f00",
        color: "#fff"
      });
      //
      let lthis = this;
      this.teamnow = data;
      lthis.leave = false;
      $.ajax({
        type: "POST",
        url: "http://91.201.54.125:5000/users",
        async: false,
        data: {
          users_id: data[2]
        },
        success: function(data) {
          lthis.ids = data.all;
          // console.log(data);
        }
      });
      // проходит по всем пользователяс
      for (var i = 0; i < lthis.ids.length; i++) {
        // смотрит капитан ли ты
        if (lthis.ids[i][0][0] == lthis.inform.id && lthis.inform.id != lthis.teamnow[6]) {
          // ты можешь выйти
          lthis.leave = true;
        }
      }
    },
    //
    async ExitChampionat() {
      let lthis = this;
      let arr = [];

      // setTimeout(function() {}, 0)

      $.ajax({
        type: "POST",
        url: "http://91.201.54.125:5000/exitFromChampionat",
        CrossDomain: true,
        async: false,
        data: {
          user_id: lthis.inform.id,
          championat_id: lthis.championatinfo[0],
          teamid: lthis.teamid
        },
        success: function(data) {
          lthis.notificationfunc(data)
          if (data == 'участник был капитаном, поэтому и команда удалена!') {
            setTimeout(function() {
              arr.push(lthis.championatinfo[7])
              for (var i = 0; i < lthis.championatinfo[7].split(',').length; i++) {
                if (lthis.championatinfo[7].split(',')[i] == lthis.inform.id) {
                  lthis.championatinfo[7] = arr[0].split(',').splice(0, i).join();
                }
              }
            }, 0)
            setTimeout(function() {
              lthis.championatsdataajax(lthis.championatinfo)
            }, 0)
            lthis.invatetrue = false;
            lthis.countteam = this.countteam - 1;
            lthis.addto = false;

            lthis.AddTooTeamFunc()
          }

          if (data == 'Пользователь удален из чемпионата!') {
            setTimeout(function() {
              // // // console.log(lthis.championatinfo[7].split(','));
              let arr = [];
              arr.push(lthis.championatinfo[7])
              for (var i = 0; i < lthis.championatinfo[7].split(',').length; i++) {
                if (lthis.championatinfo[7].split(',')[i] == lthis.inform.id) {
                  lthis.championatinfo[7] = arr[0].split(',').splice(0, i).join();
                }
              }
            }, 0)
            setTimeout(function() {
              lthis.championatsdataajax(lthis.championatinfo)
            }, 0)
            setTimeout(function() {
              lthis.AddTooTeamFunc()
            }, 0)
            lthis.addto = false;
          }
        }
      });
    },

    async addteamUser() {
      let lthis = this;
      $.ajax({
        type: "POST",
        url: "http://91.201.54.125:5000/addteamUser",
        CrossDomain: true,
        async: false,
        data: {
          id: this.inform.id,
          championat_id: this.championatinfo[0]
        },
        success: function(data) {
          let arr = [data.all];

          if (lthis.userschampionats == '') {
            lthis.userschampionats = arr;
          } else {
            let arch = []
            arch.push(lthis.championatinfo[7])
            arch = arch + ',' + lthis.inform.id
            lthis.championatinfo[7] = arch;
            // lthis.userschampionats.push(arr);
            lthis.championatsdataajax(lthis.championatinfo)
          }

          lthis.addto = !lthis.addto;
          // lthis.reset()
        }
      });
    },
    async AddToTeamFunc() {
      let letThis = this;

      $.ajax({
        type: "POST",
        url: "http://91.201.54.125:5000/AddToTeam",
        CrossDomain: true,
        async: false,
        data: {
          team_name: $("#team_name").val(),
          team_discribtion: $("#team_discribtion").val(),
          championat_id: this.championatinfo[0],
          team_nomination: $("#team_nomination").val(),
          captain_id: letThis.inform.id,
        },
        success: function(data) {
          // console.log(data);
          if (data == 'Имя команды занято') {
            letThis.notificationfunc('Имя команды занято')
          }
          $("#addTeam").removeClass("addTeamVisible");
          if (data == 'Команда создана!') {
            letThis.notificationfunc('Команда создана!')
            letThis.AddTooTeamFunc()
          }
        }
      });
    },
    async invitetoteam() {

      let lthis = this;
      $("#addUserTeam").removeClass("addTeamVisible");
      $.ajax({
        type: "POST",
        url: "http://91.201.54.125:5000/invatetoadd",
        CrossDomain: true,
        async: false,
        data: {
          nomination: $("#nomination").val(),
          coverletter: $("#coverletter").val(),
          nameteam: lthis.teamsforinv,
          nameuser: lthis.iduser
        },
        success: function(data) {
          lthis.championatsdataajax(lthis.championatinfo)
        }
      });
    },
    championatMethod() {
      this.$emit('Championats', 'st');
    },
    async AddTooTeamFunc() {
      let lthis = this;
      $.ajax({
        type: "POST",
        url: "http://91.201.54.125:5000/teamsall",
        CrossDomain: true,
        async: false,
        data: {
          championat_id: this.championatinfo[0]
        },
        success: function(data) {
          lthis.teamsch = data;
          lthis.countteam = lthis.teamsch.all.length;
          lthis.teamsinfo()
          // ,teamsinfo()
        }
      });
    },
    reset() {
      this.$emit('reset', '');
    },
    addtofunc() {
      this.addto = !this.addto;
    },
    BlockFun(id) {
      // idblock = id;
      this.$emit('BlockFun', id);
      // this.$emit('BlockFun', idblock);
    },
    championshipFun() {
      this.show = !this.show;
    },
    in_array(value, array) {
      for (var i = 0; i < array.length; i++) {
        if (value == array[i][1]) return false;
      }
      return true;
    },
    async championatsdataajax(data) {
      this.invatetrue = false;
      this.userschampionats = '';
      this.addto = false;
      this.championatinfo = data;
      let lthis = this;
      // console.log(lthis.championatinfo[0]);

      setTimeout(function() {


        $.ajax({
          type: "POST",
          url: "http://91.201.54.125:5000/usersinchampionat",
          async: false,
          data: {
            users_id: data[7],
            championat_id: lthis.championatinfo[0]
          },
          success: function(data) {
            lthis.userschampionats = data.all;

          }
        });
        for (var i = 0; i < data[7].split(',').length; i++) {
          if (data[7].split(',')[i] == lthis.inform.id) {
            lthis.addto = true;
          }
        }
        // $.ajax({
        //   type: "POST",
        //   url: "http://91.201.54.125:5000/teamsall",
        //   CrossDomain: true,
        //   async: false,
        //   data: {
        //     championat_id: lthis.championatinfo[0]
        //   },
        //   success: function(data) {
        //     lthis.teamsch = data;
        //     lthis.countteam = lthis.teamsch.all.length;
        //   }
        // });
        lthis.AddTooTeamFunc()
      }, 0)
      lthis.show = false;
    }
  },
};
</script>

<style scoped>
.notification {
  width: 400px;
  height: 100px;
  padding: 8px;
  background: #fff;
  border-radius: 5px;
  position: absolute;
  right: 20px;
  top: 80px;
  z-index: 20;
  box-shadow: 0px 0px 10px 0px #0001;
}

.title-notification {
  width: 100%;
  margin: 10px;
}

.content-notification {
  width: 100%;
  margin: 0;
  margin-left: 10px;
  margin-top: -10px;
}

.blockT {
  width: 270px;
  height: 360px;
  background: #fff;
  border-radius: 10px;
  margin: 15px;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  flex-wrap: wrap;
  cursor: pointer;
  position: relative;
}

.data-title-o {
  position: absolute;
  background: #fff;
  width: 170px;
  opacity: 0;
  vertical-align: middle;
  text-align: center;
  transition: all 0.4s;
}

.data-title-o::after {
  content: '';
  position: absolute;
  border: 8px solid transparent;
  border-top: 8px solid #eeeff2;
  margin-left: 55px;
}


.blockT:hover .data-title-o {
  opacity: 1;
  height: auto;
  border-radius: 5px;
  background: #eeeff2;
  margin-top: 30%;

}

.pick {
  border-radius: 10px;
  width: 270px;
  height: 240px;
  display: flex;
}

.title {
  font-size: 30px;
  font-weight: bold;
}

.pick img {
  width: 270px;
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;

}

.title-text,
.link-text,
.data-text {
  width: 100%;
  padding-left: 15px;
}

.link-text p {
  color: #939497;
  font-size: 15px;
  margin-top: -10px;
}

.data-text {
  margin-top: -24px;
  font-size: 14px;
  color: #5c5c5c;
  /* font-weight: 550 */
}

.blockT:hover a {
  color: #ff8001;
}

.title-text p {
  color: #000;
  font-size: 16px;
  font-weight: 600;
}

.component-fade-enter-active,
.component-fade-leave-active {
  transition: opacity 0.4s ease;
}

.component-fade-enter {
  opacity: 0;
}

.component-fade-leave-to {
  opacity: 0;
}

h1 {
  height: 20px;
}

.testing {
  width: 100%;
  /* padding-bottom: 90px; */
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  align-items: flex-start;
  padding-top: 60px;
}

.Tests {
  width: 1200px;
  position: absolute;
  display: flex;
  justify-content: flex-start;
  flex-wrap: wrap;
  margin: auto -1.5%;
  height: auto;
  padding-bottom: 50px;
}

h1 {
  width: 100%;
  margin-left: 15px;
}

@media screen and (max-width: 1350px) {
  .Tests {
    width: 90%;
  }
}

@media screen and (max-width: 800px) {
  h1 {
    text-align: center;
  }

  .Tests {
    width: 95%;
  }
}


#nameteam {
  color: #0007;
  user-select: none;
}

img {
  vertical-align: middle;
  align-items: center;
}

.addTeam {
  width: 500px;
  height: 640px;
  position: absolute;
  top: 50%;
  left: 50%;
  z-index: 9;
  margin: -330px 0 0 -250px;
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
  transform: translateY(-500px);
}

.addTeamVisible {
  visibility: visible;
  opacity: 1;
  transform: translateY(0px);
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
  font-family: roboto, sans-serif;
  resize: none;
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

.championshipMore {
  width: 100%;
  min-height: calc(100vh - 60px);
  position: relative;
}

.participants {
  display: flex;
  justify-content: flex-start;
  flex-wrap: wrap;
  margin: auto -2%;
  text-decoration: none !important;

}

.ab p {
  margin-right: 10px;
  font-weight: 550;
  font-size: 16px;
  margin-top: 4px;
  padding-bottom: 13px;
  margin-right: 50px;
  cursor: pointer;
}

.ab {
  display: flex;
  justify-content: flex-start;
}

.ab-w {
  border-bottom: solid 2px #c4cbd1;
  height: 26px;
}

hr {
  color: #c4cbd1;
  margin-top: -5px;
}

.title-link {
  font-size: 15px;
  font-weight: 550;
  transform: translateY(7px);
}

.par {
  color: #ff9123;
  cursor: pointer;
}

.partic {
  cursor: pointer;
  color: #818283;
}

.default {
  color: #909193;
}

.namech {
  border-bottom: 5px solid #ff7f00;
  color: #000;
}

.teams {
  border-bottom: 5px solid #ff7f00;
  color: #000;
}

.temsblock {
  display: flex;
  justify-content: flex-start;
  flex-wrap: wrap;
  margin: auto -1%;
}

.blocks {
  margin: 1%;
  width: 48%;
  /* min-height: 100px; */
  overflow-y: auto;
  height: calc(100vh - 270px);


}

.bl {
  height: 130px;
}

.blocks1 {
  margin: 1%;
  width: 48%;
  min-height: 100px;
}

.blocks1 h3 {
  text-align: left;
  width: 100%;
}

.addteam {
  background: rgba(255, 0, 0, 0);
  border-radius: 10px;
  border: 2px solid #f6b778;
  margin-top: 10px;
  height: 80px;
  width: 98%;
  font-weight: bold;
  color: #ff7f00;
  font-size: 16px;
  font-family: roboto, sans-serif;
}

.participant {
  width: 47%;
  height: 75px;
  border-radius: 10px;
  background: #fff;
  margin-left: 2%;
  margin-top: 1%;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  text-decoration: none;
  position: relative;
  transition: width 0.3s;

  /* flex-wrap: wrap; */
}

.participant-btn {
  width: 47%;
  height: 75px;
  border-radius: 10px;
  border: solid 2px #ff7f00;
  color: #ff7f00;
  text-align: center;
  font-size: 18px;
  margin-left: 2%;
  margin-top: 1%;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #eeeff2;
}

.participant img {
  width: 10%;
  border-radius: 100px;
  width: 60px;
  height: 60px;
}

.participant-text {
  margin-left: 20px;
  margin-top: 0px;
}

.participant-text h3 {
  font-size: 16px;
}

.participant-text p {
  margin-top: -10px;
  font-size: 14px;
  color: #797979;
  font-weight: 550;
}

.half {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  padding-left: 20px;
  position: relative;
  width: 95%;
  text-decoration: none;
}

.invite {
  display: block;
  font-size: 14px;
  font-weight: 550;
  color: #ff9123;
  cursor: pointer;
  position: absolute;
  right: 20px;
  top: 20%;
}

.blockteams {
  width: 98%;
  margin-top: 10px;
  height: 145px;
  background: #fff;
  border-radius: 10px;
  padding-left: 20px;
  padding-right: 20px;
  padding-top: 1px;
  transition: all 0.3s;
}

.addd {
  transform: translateY(-17px);
  font-size: 13px;
  font-weight: bold;
}

.ab {
  line-height: 1.5em;
  transform: translateY(-21px);
}

.blockteamsP {
  width: 100%;
  height: 75px;
  background: #fff;
  border-radius: 10px;
  display: flex;
  justify-content: аflex-start;
  align-items: center;
  margin-top: 10px;
}

.leave {
  margin-top: 10px;
  width: 100%;
  height: 75px;
  border-radius: 10px;
  border: 2px solid #ff7f00;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #ff7f00;
  font-size: 18px;
  cursor: pointer;
}

.blockteamsP img {
  width: 10%;
  border-radius: 100px;
  width: 60px;
  height: 60px;
  margin-left: 15px;
}

.name p {
  transform: translateY(6px);
  font-weight: bold;
}

.fio p {
  transform: translateY(-4px);
  font-size: 14px;
  color: #818181;
}

.all {
  margin-left: 15px;
}

@media screen and (max-width: 800px) {
  .participant {
    width: 95%;
    margin-left: 4.5%;
  }

  .participant-btn {
    width: 95%;
    margin-left: 4.5%;
  }

  .participants {
    width: 100%;
  }

  .bl {
    padding-left: 18px;
    height: 140px;
  }

  .testing {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    align-items: flex-start;
  }

  .Tests {
    width: 100%;
    display: flex;
    justify-content: center;

  }

  .addTeam {
    width: 100vw;
    /* height: calc(100vh - (60px + 50px)); */
    height: calc(100vh - 60px);
    position: absolute;
    top: 0;
    left: 0;
    margin: 0;
    padding: 0;
  }


}
</style>
