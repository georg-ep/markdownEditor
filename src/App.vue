<template>
  <div id="app">
    <v-container class="main">
      <v-row>
        <v-col class="editor">
          <h1>Source</h1>
          <v-row class="navbar">
            <div class="tab">
              <p @click="addHeader1">Header 1</p>
            </div>
            <div class="tab">
              <p @click="addHeader2">Header 2</p>
            </div>
            <div class="tab">
              <p @click="addHeader3">Header 3</p>
            </div>
            <div class="tab">
              <p @click="bold">Bold</p>
            </div>
            <div class="tab">
              <p @click="italic">Italic</p>
            </div>
            <div class="tab">
              <p @click="column2">Table row</p>
            </div>
            <div class="tab">
              <p @click="table">Table</p>
            </div>
            <div class="tab">
              <p @click="quote">Quote</p>
            </div>
            <div class="tab">
              <p @click="column2">2 Columns</p>
            </div>
            <div class="tab">
              <p @click="link">Link</p>
            </div>
            <div class="tab" data-app>
              <Modal @upload="upload" @addItem="addItem" name="Image" :items="images" />
            </div>
            <div class="tab">
              <Modal @upload="upload" @addItem="addItem" name="Video" :items="videos" />
            </div>
            <div class="tab">
              <Modal @upload="upload" @addItem="addItem" name="File" :items="files" />
            </div>
          </v-row>

          <textarea
            id="ta"
            @input="compile"
            v-model="markdownSource"
            style="width:100%; background-color:white;padding:30px;height:1000px;"
          ></textarea>
        </v-col>

        <v-col class="markdown">
          <h1>Compiled</h1>
          <br />
          <v-container style="margin-top:30px">
            <MarkdownDisplay :markdown="markdown" />
          </v-container>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import MarkdownDisplay from "./components/MarkdownDisplay";
import Modal from "./components/Modal.vue";
import markdown from "./compiled";
import markdownSource from "./source";

export default {
  name: "App",
  components: {
    MarkdownDisplay,
    Modal,
  },
  methods: {
    upload(type) {
      if (type.name === "Image") {
        this.images.push({ id: this.images.length, url: type.url });
      } else if (type.name === "Video") {
        this.videos.push({ id: this.videos.length, url: type.url });
      } else {
        this.files.push({ id: this.files.length, url: type.url });
      }
    },
    insertAtCaret(text) {
      const el = document.getElementById("ta");
      var caretPos = el.selectionStart;
      var textAreaTxt = el.value;
      el.value =
        textAreaTxt.substring(0, caretPos) +
        text +
        textAreaTxt.substring(caretPos);
    },

    compile() {
      String.prototype.replaceAt = function (index, replacement, amount) {
        return (
          this.substr(0, index) +
          replacement +
          this.substr(index - amount + replacement.length)
        );
      };

      this.markdownSource = document.getElementById("ta").value;
      this.markdown = this.markdownSource;
      this.markdownSource = this.markdownSource.replaceAll("", "");
      this.markdown = this.markdown.replaceAll("[/col-left]", "</div>");
      this.markdown = this.markdown.replaceAll("[/col-right]", "</div>");
      this.markdown = this.markdown.replaceAll("[/row]", "</div>");
      this.markdown = this.markdown.replaceAll("[/table]", "</table>");
      this.markdown = this.markdown.replaceAll("[/quote]", "</q>");
      this.markdown = this.markdown.replaceAll("[quote]", "<q>");
      this.markdown = this.markdown.replaceAll("/]", ">");
      this.markdown = this.markdown.replaceAll("[img=", "<img src=");
      this.markdown = this.markdown.replaceAll(
        "[video=",
        `<video><source src="`
      );

      this.markdown = this.markdown.replaceAll(
        "[col-left]",
        '<div class="column">'
      );
      this.markdown = this.markdown.replaceAll(
        "[col-right]",
        '<div class="column">'
      );
      this.markdown = this.markdown.replaceAll("[row]", '<div class="row">');
      this.markdown = this.markdown.replaceAll("[table]", "<table>");
    },

    link() {
      this.insertAtCaret(`<br>This is a [link](https://exampler.com)\n\n`);
      this.compile();
    },

    quote() {
      this.insertAtCaret(`[quote]Quote Here[/quote]\n\n`);
      this.compile();
    },

    column2() {
      this.insertAtCaret(
        `[row][col-left]Column1[/col-left][col-right]Column2[/col-right][/row]\n\n`
      );
      this.compile();
    },
    table() {
      this.insertAtCaret("[table]\n\n[/table]\n\n");
      this.compile();
    },

    addHeader1() {
      this.insertAtCaret("# Header 1\n\n");
      this.compile();
    },
    addHeader2() {
      this.insertAtCaret("## Header 2\n\n");

      this.compile();
    },
    addHeader3() {
      this.insertAtCaret("### Header 3\n\n");
      this.compile();
    },
    bold() {
      this.insertAtCaret("**bold**\n\n");
      this.compile();
    },
    italic() {
      this.insertAtCaret("*italic*\n\n");

      this.compile();
    },
    addItem(itemData) {
      switch (itemData.name) {
        case "Image":
          this.insertAtCaret(
            '[img="' + this.images[itemData.id].url + '"/]\n\n'
          );
          this.compile();
          break;
        case "Video":
          this.insertAtCaret(
            '[video="' + this.videos[itemData.id].url + '"/]\n\n'
          );
          this.compile();
          break;
        case "File":
          this.insertAtCaret(
            '[file="' + this.files[itemData.id].url + '"]\n\n'
          );
          this.compile();
          break;
        default:
          return false;
      }
    },
  },

  data() {
    return {
      markdown,
      markdownSource,
      images: [
        {
          id: 0,
          url: "https://www.istockphoto.com/photo/beautiful-nature-at-morning-in-misty-spring-forest-with-sun-gm506856658-84421597",
        },
        {
          id: 1,
          url: "https://www.facebook.com/",
        },
      ],
      videos: [
        {
          id: 0,
          url: "https://www.youtube.com/",
        },
        {
          id: 1,
          url: "https://www.movies.com/",
        },
      ],
      files: [
        {
          id: 0,
          url: "https://file1.com",
        },
        {
          id: 1,
          url: "https://www.file2.com",
        },
      ],
    };
  },
};
</script>

<style scoped>
.tab {
  margin-right: 20px;
}
.navbar {
  padding: 20px;
  padding-left: 20px;
}
.editor {
  width: 100%;
  background-color: beige;
  padding: 50px;
}
.markdown {
  background-color: aquamarine;
  padding: 50px;
}
</style>