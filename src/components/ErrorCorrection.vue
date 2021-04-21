<template>
    <div class="ec">
        <transition name="ec__modal">
            <div class="ec__modal-mask">
                <div class="ec__modal-wrapper">
                    <div class="ec__modal-container">
                        <button class="ec__button-close" @click="closeModal">
                            Закрыть
                        </button>
                        <form
                            @submit.prevent="submit"
                            action=""
                            method="POST"
                            class="ec__form"
                        >
                            <label>
                                <input type="text" placeholder="E-mail" class="ec__form-input ec__input" v-model="postBody.email">
                            </label>
                            <div class="ec__warning">Введите корректный email</div>
                            <label>
                                <textarea v-model="postBody.textarea" maxlength="100" name="" id="" cols="30" rows="10" placeholder="Описание проблемы" class="ec__form-input ec__form-textarea"></textarea>
                            </label>
                            <div class="ec__warning_description">Опишите проблему</div>
                            <button type="submit" class="ec__modal-default-button" v-on:click="sendForm">
                                Отправить
                            </button>
                        </form>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>


function addWidgetTag () {
    let widget = document.createElement('vue-widget');
    document.body.appendChild(widget);
}
addWidgetTag()

let div = null;
let selection = window.getSelection();
import axios from 'axios';

export default {
    name: 'HelloWorld',
    data () {
        return {
            selection: window.getSelection(),
            isModalVisible: false,
            postBody: {
                email: '',
                textarea: '',
                coordinates: "",
                time: '',
                text: '',
                resolution: '',
                url: '',
                ip_user: '',
                type_device: '',
                user_agent: '',
            }
        }
    },
    methods: {
        addCssToDom: function (url) {
            let style = document.createElement('link');
            style.rel   = 'stylesheet';
            style.type  = 'text/css';
            style.href  = url;
            document.head.appendChild(style);
        },
        initializationTag: function () {
            this.addCssToDom('https://grishov.ru/css/app.css');
        },
        closePrompt: function (event) {
            if (event.target.className == 'ec__block-tooltip') {
                let block_tooltip = document.querySelector('.ec__block-tooltip');
                block_tooltip.style.display = "none";
            }
        },
        closeForm: function (event) {
            let modal_mask = document.querySelector('.ec__modal-mask')
            if (event.target.className == 'ec__modal-mask') {
                modal_mask.style.display = 'none'
            }
        },
        getIp: function () {
            fetch('https://api.ipify.org/?format=json')
                .then(results => results.json())
                .then(
                    response => {
                        this.some(response)
                    }
                );
        },
        some: function (data) {
            var script_tag = document.querySelector("script");
            var src = script_tag.src
            if (src.indexOf('dbg') !== -1) {
                console.log(data.ip);
            }

            this.postBody.ip_user = data.ip;
            return data.ip;
        },
        drawBorderAroundSelection: function (event) {
            let selection = window.getSelection(), // get the selection then
                range = selection.getRangeAt(0), // the range at first selection group
                rect = range.getBoundingClientRect(), // and convert this to useful data
                modal_mask = document.querySelector('.ec__modal-mask');

            if (selection != '' && modal_mask.style.display != "flex") {
                if (div) {
                    div.parentNode.removeChild(div);
                }
                div = document.createElement('div'); // make box
                div.class = 'rect';
                // div.style.border = '2px solid black'; // with outline
                div.innerHTML = "Сообщить об ошибке"
                div.style.position = 'fixed'; // fixed positioning = easy mode
                div.classList.add('ec__block-tooltip')
                div.style.top = rect.top - 35 + 'px'; // set coordinates
                div.style.left = rect.left + 'px';
                // div.style.height = 20 + 'px'; // and size
                // div.style.width = 250 + 'px';
                document.body.appendChild(div); // finally append
                if (parseInt(div.style.left) + 200 > window.innerWidth) {
                    div.style.right = "0";
                    div.style.left = "auto";
                }
                if (parseInt(div.style.top) + 30 > window.innerWidth) {
                    div.style.right = "0";
                    div.style.left = "auto";
                }
                if (parseInt(div.style.top) < 0) {
                    div.style.top = "0";
                }

            }
            let block_tooltip = document.querySelector('.ec__block-tooltip');
            if (selection == '' && div != null) {
                block_tooltip.style.display = "none";
            }
        },
        getDeviceType: function () {
                const ua = navigator.userAgent;
                if (/(tablet|ipad|playbook|silk)|(android(?!.*mobi))/i.test(ua)) {
                    return "tablet";
                }
                if (
                    /Mobile|iP(hone|od)|Android|BlackBerry|IEMobile|Kindle|Silk-Accelerated|(hpw|web)OS|Opera M(obi|ini)/.test(
                        ua
                    )
                ) {
                    return "mobile";
                }
                return "desktop";
        },
        getUrl: function() {
            let url = window.location.href;
            return url
        },
        getTime: function() {
            let today = new Date();
            let date = today.getFullYear()+'-'+(today.getMonth()+1)+'-'+today.getDate();
            let time = today.getHours() + ":" + today.getMinutes() + ":" +
                today.getSeconds();
            let dateTime = date+' '+time;
            return dateTime;
        },
        getUserAgent: function () {
            let ua = window.navigator.userAgent;
            return ua
        },
        getScreenResolution: function() {
            let height = window.screen.availHeight;
            let width = window.screen.availWidth;
            return `${height}px : ${width}px`
        },
        showModal: function (event) {
            if (event.target.className == "ec__block-tooltip") {
                /////////////// get time
                this.getTime()
                this.postBody.time = this.getTime()

                /////////////////get resolution
                this.getScreenResolution()
                this.postBody.resolution = this.getScreenResolution();

                ///////////////get user agent
                this.getUserAgent()
                this.postBody.user_agent = this.getUserAgent();

                //////////////get url
                this.getUrl();
                this.postBody.url = this.getUrl();

                /////////////get ip
                this.getIp()

                //////////get type device
                this.postBody.type_device = this.getDeviceType();


            }
        },
        showDebugInfo: function () {
            var script_tag = document.querySelector("script");
            var src = script_tag.src

            if (src.indexOf('dbg') !== -1) {
                console.log(this.getDeviceType());
                console.log(this.getUrl());
                console.log(this.getTime());
                console.log(this.getCoordinates());
                console.log(this.getScreenResolution());
                console.log(this.getUserAgent());
                console.log(this.getText());
            }
        },
        getCoordinates: function() {
            let range = selection.getRangeAt(0);
            let rect = range.getBoundingClientRect();
            let x = rect.top;
            let y = rect.left;
            return `x:${x} : y:${y}`
        },
        getText: function() {
            return selection.toString();
        },
        leaveSelection: function (event) {
            if (event.target.className == "ec__block-tooltip") {
                ///////////// get coordinates
                this.getCoordinates()
                this.postBody.coordinates = this.getCoordinates();

                /////////// get text
                this.getText()
                this.postBody.text = this.getText();

                this.showDebugInfo()
            }
        },
        closeModal: function () {
            let mask = document.querySelector('.ec__modal-mask');
            mask.style.display = "none"
        },
        openModal: function (event) {
            if (event.target.className == "ec__block-tooltip") {
                let mask = document.querySelector('.ec__modal-mask');
                mask.style.display = "flex"
            }
        },
        checkForm: function(event) {
            if (event.target.className == "ec__form-input ec__input") {
                ///////////validation email
                var reg = /^([A-Za-z0-9_\-\.])+\@([A-Za-z0-9_\-\.])+\.([A-Za-z]{2,4})$/;
                var address = document.querySelector('.ec__input').value;

                var warning = document.querySelector('.ec__warning');

                if(reg.test(address) == false) {
                    warning.style.display = "block"
                    return false
                } else {
                    warning.style.display = "none"
                    return true
                }

            }
            if (event.target.className == "ec__form-input ec__form-textarea") {
                let textarea_warning = document.querySelector(".ec__warning_description");
                if (textarea_warning.value != '') {
                    textarea_warning.classList.remove("textarea-block");
                }
            }

        },
        sendForm: function(event) {
            var reg = /^([A-Za-z0-9_\-\.])+\@([A-Za-z0-9_\-\.])+\.([A-Za-z]{2,4})$/;
            var address = document.querySelector('.ec__input').value;
            var value_inp = document.querySelector('.ec__form-input.ec__input');

            let textarea_warning = document.querySelector(".ec__form-input.ec__form-textarea");
            if (textarea_warning.value == '') {
                let warning_desc = document.querySelector('.ec__warning_description')
                warning_desc.classList.add("textarea-block");
            }

            if (value_inp.value == "") {
                let warning_inp = document.querySelector(".ec__warning");
                warning_inp.style.display = "block";
            } else {
                if(reg.test(address) &&  textarea_warning.value != '') {
                    const str = JSON.stringify(this.postBody);
                    let config = {

                    }



                        axios.post('https://led.43rm.ru/test_rest.php', str, config)
                            .then((response) => {
                                var script_tag = document.querySelector("script");
                                var src = script_tag.src
                                if (src.indexOf('dbg') !== -1) {
                                    console.log(response);
                                }
                            })
                            .catch((error) => {
                                console.log(error.response);
                            });

                    let modal_mask = document.querySelector('.ec__modal-mask');
                    modal_mask.style.display = "none";

                    alert('форма отправлена')
                }
            }

        },
        submit() {

        }
    },
    created() {
        document.addEventListener("mouseup", this.drawBorderAroundSelection);
        document.addEventListener("click", this.showModal);
        document.addEventListener('mousedown', this.leaveSelection);
        document.addEventListener("click", this.openModal);
        document.addEventListener("click", this.closeForm);
        document.addEventListener("click", this.closePrompt);
        document.addEventListener("keyup", this.checkForm);
    },
    beforeMount() {
        this.initializationTag()
    },
}

</script>

