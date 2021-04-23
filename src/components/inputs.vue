<template>
  <div class="container">
    <h1 class="is-size-2">Copy Mappings</h1>
    <div class="section pb-0 pt-2">
      <ol style="list-style-type: none; text-align: left" class="list">
        <li class="list-item">Use Firefox</li>
        <li>Go to page you want to use as master</li>
        <li>open up dev tools go to networking</li>
        <li>
          update mapping then change it back and save it to create POST request
        </li>
        <li>use the dropdown to find the pdf you want to copy to</li>
        <li>Make sure the fields are the same</li>
        <li>Copy the JSON from the POST request into the POST input</li>
        <li>Copy the JSON from the GET response into the GET input</li>
        <li>Click Create Post Request</li>
        <li>Click Copy to Clipboard to select all and copy it</li>
        <li>
          In Firefox dev tools Right Click POST request Select Edit and Resend
        </li>
        <li>
          Change the path to have the correct document template should match the
          number on your GET REQUEST
        </li>
        <li>Delete Request and paste in the one you copied</li>
        <li>click send to send the new request should get back a 200</li>
      </ol>
    </div>
    <section class="section">
      <div>
        <label class="label pb-2">
          POST request main file
          <textarea v-model="valuesToCopy" class="textarea"></textarea>
        </label>
      </div>
      <div>
        <button @click="fieldList = ''" class="button is-primary">
          reset get
        </button>
        <label class="label pb-2">
          GET response results new mappings
          <textarea v-model="fieldList" class="textarea"></textarea>
        </label>
      </div>
      <div>
        <button @click="finalResults = ''" class="button is-primary">
          reset final
        </button>
        <label class="label pb-2">
          final request
          <textarea
            ref="finalJson"
            v-model="finalResults"
            class="textarea"
          ></textarea>
        </label>
      </div>
      <div>
        <button @click="parseJson" class="button is-primary">
          Create Post Request
        </button>
        <button @click="copyToClipboard" class="button is-secondary p-3">
          Copy to Clipboard
        </button>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      valuesToCopy: "",
      fieldList: "",
      path: "",
      documentTemplateId: "",
      finalResults: "",
    };
  },
  computed: {
    fields() {
      if (this.fieldList) {
        return JSON.parse(this.fieldList);
      }
      return "";
    },
    otherValues() {
      if (this.valuesToCopy) {
        return JSON.parse(this.valuesToCopy);
      }
      return "";
    },
    newTemplateId() {
      if (this.fields?.results?.length) {
        return this.fields.results[0].documentTemplateId;
      }
      return "";
    },
  },
  methods: {
    parseJson() {
      if (!this.fieldList || !this.valuesToCopy) {
        return;
      }

      const valuesDictionary = this.fields.results.reduce(
        (map, obj) => (
          (map[obj.fieldName] = obj.documentTemplateQuestionId), map
        ),
        {}
      );

      const finalResults = this.otherValues.request.map(
        ({
          documentTemplateId,
          documentTemplateQuestionId,
          fieldName,
          ...rest
        }) => {
          return {
            documentTemplateId: this.newTemplateId,
            documentTemplateQuestionId: valuesDictionary[fieldName],
            fieldName,
            ...rest,
          };
        }
      );

      this.finalResults = JSON.stringify({ request: finalResults });
    },
    copyToClipboard() {
      const requestToCopy = this.$refs.finalJson;
      requestToCopy.select();
      document.execCommand("copy");
    },
  },
};
</script>