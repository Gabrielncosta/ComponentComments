<template>
<div class="app">
  <div class="row">
    <div class="col-md-12">
      <form class="form">
        <div class="row">
          <div class="col-md-12">
            <input v-model="name" class="comment__input" type="text" placeholder="Nome">
          </div>
          <div class="col-md-12">
            <input v-model="email" @focus="focuscomment" class="comment__input" type="email" placeholder="Email *">
          </div>
        </div>

        <div class="row">
          <div class="col-md-12">
            <input v-model="title" class="comment__input" type="email" placeholder="Título">
          </div>
          <div class="col-md-12">
            <textarea v-model="comment" class="comment__text comment__input" rows="10" placeholder="Comentário *" required></textarea>
          </div>
        </div>
        <div class="row">
          <div class="col-md-12 mt-3">
            <button @click="add_comments" class="comment__btn" id="send" type="button">POSTAR</button>
          </div>
        </div>

        <div class="row">
          <div class="col-md-12 mt-2 text-center mt-2 ml-1">
            <p class="estrelas my-auto">Avalie o produto, quando sua compra for finalizada sua avaliação será exibida junto de seu comentário.</p>
          </div>
          <div class="col-md-3 mt-1 mx-auto">
            <star-rating :rating="5" :increment="0.5" :star-size="40" :show-rating="false"></star-rating>
          </div>
        </div>
      </form>
    </div> 
  </div>

<template v-if="visited">
  <div class="list">
    <div v-for="(comment, i) in visitedArray" :key="i" class="comment__user-wrapper">
      <div class="item">
        <div class="photo">
          <img src="../assets/pf.png">
        </div>
        <div class="info">
          <div class="name">
            <p>{{ comment.name }} - Postado às {{ comment.date }}</p>
          </div>
          
          <p class="titulo">{{ comment.title }} <span class="verified"> <br> Pendente de verificação </span> </p>
          <div class="comment">
            <p> {{ comment.comment }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

  <div class="list">
    <div v-for="(comment, i) in comments" :key="i" class="comment__user-wrapper">
      <div class="item">
        <div class="photo">
          <img src="../assets/pf.png">
        </div>
        <div class="info">
          <div class="name">
            <p>{{ comment.name }} - Postado às {{ comment.date }}</p>
          </div>
          <p class="titulo">{{ comment.title }} <span class="verified"> <br> Pendente de verificação </span> </p>
          <div class="comment">
            <p> {{ comment.comment }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="list">
    <div v-for="(comment, i) in json" :key="i" class="comment__user-wrapper">
      <div class="item">
        <div class="photo">
          <img src="../assets/pf.png">
        </div>
        <div class="info">
          <div class="name">
            <p>{{ comment.name }} - Postado às {{ comment.date }}</p>
          </div>
          <div class="row">
            <div class="col-md-12">
              <star-rating :rating="comment.rating" :read-only="true" :increment="0.01" :star-size="20" :show-rating="false"></star-rating>
              <p class="titulo">{{ comment.title }} <span class="verified"> <br> Compra verificada </span> </p>
            </div>
          </div>
          <div class="comment">
            <p> {{ comment.comment }}</p>
          </div>
        </div>
      </div>
    </div>

    <div class="col-md-12 text-center mb-4">
      <button type="button" class="btn btn-secondary" @click="loadComments">Carregar mais comentários</button>
      <p class="mb-5 mt-2 warn" v-if="!loaded">Não há mais comentários!</p>
    </div>
  </div>
    
  </div>



</template>

<script>
import myJson from '../data/db.json';

export default {
  data() {
    return {
      name: "",
      email: "",
      comment: "",
      date: "",
      counter: 0,
      comments: [],
      json: [],
      visitedArray: [],
      loaded: true,
      visited: '',
      rating: [],
      title: '',
      database: [],
    };
  },


  created() {

    let jsonSize = Object.keys(myJson).length;

    // db.json comments
    
    for(let i = 0; i < Math.ceil(jsonSize) / 3; i++) {
      this.json.push({
        name: myJson[i].nome, comment: myJson[i].comentário, date: this.newDate(), rating: myJson[i].avaliação,
        título: myJson[i].title,
      });
    } 
    
    
    let get = localStorage.getItem(this.counter);
    let getparsed = JSON.parse(get);
    let y = getparsed.length;
    
    for(let i = 0; i < y; i++) {
      this.comments.push({
        name: getparsed[i].name, comment: getparsed[i].comment, date: getparsed[i].date, title: myJson[i].title,
      });
    }

    let visited = localStorage.getItem('visited')

    if(!visited) {
        this.comments.unshift({
          name: myJson[jsonSize -2].nome, comment: myJson[jsonSize -2].comentário, date: myJson[jsonSize -2].data,
          title: myJson[jsonSize-2].título,
        });
        localStorage.setItem(0, JSON.stringify(this.comments));
        this.counter++
        localStorage.setItem('visited', true);
        console.log('counter do visited: ' + this.counter);
    }

    this.sortedItems();
  },

  methods: {

    focuscomment() {
        let jsonSize = Object.keys(myJson).length;
        let check = localStorage.getItem('checked');
        
        setTimeout(() =>{
          if(!check) {
            this.comments.unshift({
              name: myJson[jsonSize -1].nome, comment: myJson[jsonSize -1].comentário, date: myJson[jsonSize - 1].data,
              title: myJson[jsonSize -1].título,
            });
            localStorage.setItem(0, JSON.stringify(this.comments));
            this.counter++
            localStorage.setItem('checked', true);
            check = true;
            myJson[jsonSize - 1].checked = true;
          }
        }, 2000);
    },

    add_comments() {
      //this.date = new Date().toISOString().split('T')[0];
      if(!this.comment || !this.name) {
        return 0;
      }
      this.comments.unshift({ name: this.name, comment: this.comment, date: this.getDate(), title: this.title });
      localStorage.setItem(0, JSON.stringify(this.comments));
      this.name = "";
      this.email = "";
      this.comment = "";
      this.title = "";
      //this.date = "";
      this.counter++;
      console.log('counter do addcomment: ' + this.counter);
      let visited = localstorage.getItem('visited');
    },

/*     addjson() {
      this.myJson.unshift({ name: this.name, comment: this.comment, date: this.getDate(), title: this.title });
      localStorage.setItem(0, JSON.stringify(this.comments));
    },
 */
    setRating: function(rating){
      this.rating= rating;
    },

    getDate() {
      let data = new Date();
      let dia = data.getDate()+ '/' + (data.getMonth() + 1) + '/' + data.getFullYear();
      let hora = data.getHours() + ':' + data.getMinutes() + ' de ' + dia;
      return hora;
    },

    newDate() {
      let data = new Date();
      let rand = Math.floor(1 + Math.random() * (50 + 1 - 1));
      let randays = Math.floor(1 + Math.random() * (10 + 1 - 1));
      let dia = data.getDate() - randays
      //let dia = (data.getDate() - randays)+ '/' + (data.getMonth() + 1) + '/' + data.getFullYear();
      let month = data.getMonth() + 1
      let year = data.getFullYear();
      if(dia < 1 ){
        dia = Math.abs(dia) + 1;
        year = year - 1;
      }
      dia = dia + '/' + month + '/' + year;
      // let hora = data.getHours() + ':' + (data.getMinutes() - rand )+ ' de ' + dia;
      let hora = data.getHours()
      let minutes = data.getMinutes() - rand
      if(minutes < 1) {
        minutes = Math.abs(minutes);
        hora = hora - 1;
      }
      return hora + ':' + minutes + ' de ' + dia
    },

    loadComments() {
      let jsonSize = Object.keys(myJson).length;

      if (this.loaded) {
        for(let i = Math.ceil(jsonSize / 3); i < jsonSize - 2; i++) {
          this.json.push({
            name: myJson[i].nome, comment: myJson[i].comentário, date: myJson[i].data, rating: myJson[i].avaliação,
          });
          this.loaded = false;
        }
      }
    },
    sortedItems() {
    alert('entrou');
      this.json.sort( ( a, b) => {
          return new Date(a.date) - new Date(b.date);
      });

      console.log('entrou')
      return this.json;
    }
  },
};
</script>


<style lang="scss">

.titulo {
  font-weight: 700;
  color: #111 !important;
  font-size: 13px;

}

.estrelas {
  display: inline;
  font-size: .8em;
  font-weight: 500;
  color: #666
}

.checked {
  color: orange;
}

.comments {
    margin: 30px 200px 0;

    .block-title {
    padding: 25px 0 15px;
    font-family: 'Montserrat';
    font-size: 25px;
    line-height: 1.2;
    text-transform: uppercase;
    border-bottom: 1px solid 
    #e1e1e1;
    margin: 0 0 20px;
    text-align: start;
  }

  .block-description {
    font-family: 'Montserrat';
    font-size: 12px;
    margin: 0 0 40px;
    text-align: start;
  }
}

.form {
  background:#f7f7f7;
  padding: 20px 15px;
  margin: 0 0 20px;
  border-radius: 5px;

  input {
    width: 100%;
    padding: 15px;
    font-family: sans-serif;
    font-size: 12px;
    border: 0;
    margin: 0 5px 10px;
    border-radius: 5px;
  }

  textarea {
    width: 100%;
    height: 45px;
    padding: 15px;
    font-family: sans-serif;
    font-size: 12px;
    border: 0;
    margin: 0 5px;
    border-radius: 5px;
  }

  button {
    background:#333;
    flex-grow: 1;
    font-family: sans-serif;
    font-size: 14px;
    font-weight: bold;
    color:
    #fff;
    text-align: center;
    text-transform: uppercase;
    border: 0;
    margin: 0 5px;
    border-radius: 5px;
    cursor: pointer;
    width: 100%;
    height: 50px;
  }
}


.list {
  text-align: start;


    .verified {
      color: #c45500;
      font-size: 11px;
      font-weight: 700;
    }
    .item {
    display: flex;
    padding: 0 0 25px;
    border-bottom: 1px solid#eee;
    margin: 0 0 25px;

      .photo {
        margin: -3px 20px 0;
        img {
          width: 25px;
        }
      }

      .name p {
        font-size: 14px;
        color:#000;
        margin-bottom: 8px;
      }
      
      .comment {
        font-size: 13px;
        color:#222;
        font-weight: 600;
        margin: 0;
      }
    }

    .warn {
      color: red;
      font-weight: 600;

    }
}
  

</style>