<script>
    /**
     * Svelte
     */
    import {createEventDispatcher} from "svelte";

    /**
     * Dispatch
     */
    const dispatch = createEventDispatcher()

    /**
     * Props
     */
    export let value = false
    export let editable = false
    export let type = "text"

    /**
     * Regex
     */
    const numberRegex = /^\d{0,10}$/

    /**
     * States
     */
    let innerValue = value
    let lastValue = null
    let content = null

    /**
     * On Blur
     * @description Обработчик потери фокуса с поля
     * @param e
     */
    const blurEvent = (e) => {
        const content = e.target.value
        const validatedContent = validateByType(content)
        if (validatedContent) {
            value = validatedContent
        } else {
            innerValue = value
        }

        dispatch("blur", {
            value
        })
    }

    /**
     * Validate By Type
     * @description Метод для валидации контента согласно типу поля
     * @param {string} content
     * @return {boolean|*}
     */
    const validateByType = (content) => {
        switch (type) {
            case "text":
                return content.trim()

            case "number":
                if (numberRegex.test(content) || ((content ?? '').length === 0)) {
                    return content.trim()
                } else {
                    return false
                }
        }
    }

    const changeEvent = (e) => {
        const content = e.target.value
        if (type === "number") {
            if (numberRegex.test(content) || ((content ?? '').length === 0)) {
                lastValue = JSON.parse(JSON.stringify(content))
                innerValue = lastValue
            } else {
                innerValue = lastValue
            }
        } else {
            lastValue = JSON.parse(JSON.stringify(content))
            innerValue = content
        }
    }

    const scrollEvent = (e) => {
        content.scrollLeft = e.currentTarget.scrollLeft
    }


</script>

<div class="input-modified">
    <div class="input-modified__content"
         bind:this={content}>
        <span>{innerValue}</span>
    </div>
    <textarea on:input={changeEvent}
              on:blur={blurEvent}
              on:scroll={scrollEvent}
              value={value}
              rows={1}
              disabled={!editable}/>
</div>

<style lang="scss">
  .input-modified {
    position: relative;
    width: 100%;
    height: max-content;

    border: 1px solid red;
    padding: 4px;

    cursor: text;

    &__content {
      position: absolute;
      left: 4px;
      top: 0;

      display: flex;
      align-items: center;

      overflow-x: scroll;
      color: black;

      width: 100%;
      height: 100%;
      white-space: nowrap;
      font-size: 14px;

      /* IE and Firefox support */
      -ms-overflow-style: none;
      scrollbar-width: none;

      span {
        font-size: 14px;
        line-height: 16px;
        color: black;
        display: inline-block;
        position: relative;
        z-index: 0;

        font-family: Roboto, sans-serif;
      }

      &::-webkit-scrollbar {
        display: none;
      }
    }

    textarea {
      background: transparent;
      color: transparent;
      resize: none;
      position: relative;
      vertical-align: middle;

      outline: none;

      font-family: Roboto, sans-serif;
      caret-color: black;
      font-size: 14px;
      line-height: 16px;

      padding: 0;

      border: unset;

      white-space: nowrap;

      width: 100%;

      &::-webkit-scrollbar {
        display: none;
      }
    }
  }
</style>