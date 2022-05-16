<template lang='pug'>
header.mb-1(class='md:mb-11')
  div(class='md:flex md:justify-between md:items-center')
    h1.text-4xl.mb-4(class='md:mb-0')
      | Lynn Benson /&nbsp;
      span.text-green-800 Drops
    button.flex.items-center.mt-2.pb-2.border-b-4.border-white(to='/' class='hover:border-green-400')
      .mr-3 More about this project
      .arrow
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"><path d="M502.6 278.6l-128 128c-12.51 12.51-32.76 12.49-45.25 0c-12.5-12.5-12.5-32.75 0-45.25L402.8 288H32C14.31 288 0 273.7 0 255.1S14.31 224 32 224h370.8l-73.38-73.38c-12.5-12.5-12.5-32.75 0-45.25s32.75-12.5 45.25 0l128 128C515.1 245.9 515.1 266.1 502.6 278.6z"/></svg>
</template>
<style lang='sass' scoped>
  h1
    text-transform: uppercase
  button
    height: 30px
  .arrow
    width: 20px
</style>
