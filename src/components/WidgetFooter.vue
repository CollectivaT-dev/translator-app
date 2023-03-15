<template>
  <div class="widget-footer">
    <div class="container">
      <div class="patrons">
        <div class="ajun">
          <div class="logosuport">
            <img class="zimg-responsive" alt="Ajuntament de Barcelona logo" src="@/assets/ajuntament.jpg"> 
          </div>
        </div>
        <div class="gene">
          <div class="logosuport">
            <img class="zimg-responsive" alt="Generalitat de Catalunya logo" src="@/assets/generalitat.png"> 
          </div>
        </div>
        <div class="genedisc" v-t="'supportgene'"></div>
      </div>
      <div class="owners">
        <div class="nar">
          <div class="logo">
            <a href="http://collectivat.cat" target="_blank">
            <img alt="Col·lectivaT logo" src="@/assets/collectivat.png">
            </a>
          </div>
        </div>
        <div class="ciemen">
          <div class="logo">
            <a href="http://ciemen.cat" target="_blank">
            <img class="zimg-responsive" alt="CIEMEN logo" src="@/assets/ciemen2.png">
            </a>
          </div>
        </div>
      </div>
      <div class="iniciativa" v-t="'iniciative'"></div>
      <div class="suport" v-t="'support'"></div>
    </div>
    <div class="disclaimer" v-html="$t('disclaimer')">        
    </div>
  </div>
</template>

<script>

export default {}
</script>

<style scoped>
.widget-footer{
  margin-top: 4rem;
}
.logo img{
  max-height: 120px;
  margin: 0 auto;
  text-align: center;
}

.logosuport img{
  max-height: 60px;
  margin: 0 auto;
  text-align: center;
}

.container {  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 0.1fr 1.9fr;
  grid-auto-columns: 1fr;
  gap: 10px 10px;
  grid-auto-flow: row;
  grid-template-areas:
    "iniciativa suport"
    "owners patrons";
}

.patrons {  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 1fr;
  gap: 0px 5px;
  grid-auto-flow: row;
  align-items: center;
  grid-template-areas:
    "ajun gene genedisc";
  grid-area: patrons;
}

.ajun {
  justify-self: start;
  grid-area: ajun;
}

.gene {
  justify-self: end;
  grid-area: gene;
}

.genedisc {
  justify-self: start;
  grid-area: genedisc;
  font-size:0.7vw;
  text-align:center;
}

.owners {  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr;
  gap: 10px 15px;
  grid-auto-flow: row;
  justify-items: end;
  grid-template-areas:
    "nar ciemen";
  grid-area: owners;
}

.nar {
  justify-self: end;
  grid-area: nar;
}

.ciemen {
  justify-self: start;
  grid-area: ciemen;
}

.iniciativa { grid-area: iniciativa; }

.suport { grid-area: suport; }


</style>