---
layout: artigo
nome: LabProg - Ajuda básica de UDK - 2014 2S
---

<h1>UDK</h1>
  <h2>Laboratório de Programação</h2>
  <div class="alert alert-danger">Não se esqueça de desligar a Global Illumination e ativar o Game Type! <span class="text-muted">porque senão vai ficar leeeeento</span> </div>
  <img src="http://i.imgur.com/73OcDot.png">
  <img src="http://i.imgur.com/G5zYIux.png">
  <img src="http://i.imgur.com/FaGXPTg.png">


  <h4>Cheatsheet</h4>
  <span class="text-muted">Como fazer aquele negócio que você nunca lembra como fazer. Clique no nome para expandir.</span><br>  


<div class="panel-group" id="accordion" role="tablist" aria-multiselectable="true">

  <!-- Colocar água -->
  <div class="panel panel-default">

      <div class="panel-heading" role="tab" id="headingOne">
        <h4 class="panel-title">
          <a class="collapsed" data-toggle="collapse" data-parent="#accordion" href="#collapseOne" aria-expanded="false" aria-controls="collapseOne">
            Como colocar água
          </a>
        </h4>
      </div>

      <div id="collapseOne" class="panel-collapse collapse in" role="tabpanel" aria-labelledby="headingOne">
        <div class="panel-body">
            <p><strong>Superfície da água</strong><br><img src="http://i.imgur.com/EDenarv.png"><br>
            <strong>Volume da água</strong><br><img src="http://i.imgur.com/uvoPEZG.png"><br>
            <strong>Água "borrada"</strong><br><img src="http://i.imgur.com/sbnouU1.png"><img src="http://i.imgur.com/7UWQmU0.png"><br>
            </p>
            <a href="http://i.imgur.com/ilUEpiF.png">Efeito final</a>
        </div>
      </div>

  </div>

<div class="panel panel-default">
    <div class="panel-heading" role="tab" id="headingTwo">
      <h4 class="panel-title">
        <a class="collapsed" data-toggle="collapse" data-parent="#accordion" href="#collapseTwo" aria-expanded="false" aria-controls="collapseTwo">
          Kismet
        </a>
      </h4>
    </div>
    <div id="collapseTwo" class="panel-collapse collapse" role="tabpanel" aria-labelledby="headingTwo">
      <div class="panel-body">
        <p>
          <strong>Static Mesh no Content Browser como InterpActor</strong><br><img src="http://i.imgur.com/6rJuWem.png"><br>
          <strong>Brush no local, como TriggerVolume</strong><br><img src="http://i.imgur.com/YtEK6X4.png"><br>
          <strong>No Kismet, nova Matinee, criar referência a Trigger</strong><br><img src="http://i.imgur.com/Muf7Zt3.png"><img src="http://i.imgur.com/KdjXC7u.png"><br>
          <strong>No Matinee, grupo, movement track e link com objeto</strong><br><img src="http://i.imgur.com/FF9aLpH.png"><img src="http://i.imgur.com/x2UvZZ6.png"><br>
                                                                                  <img src="http://i.imgur.com/cHocKJB.png"><img src="http://i.imgur.com/aOQOQcG.png">
          <strong>Selecionar track, apertar enter para cada keyframe, mover mesh</strong><br><img src="http://i.imgur.com/p9j7JBF.png"><img src="http://i.imgur.com/hUx68Uw.png"><br>
          <strong>De volta no Kismet, evento de toque, trigger infinito</strong><br><img src="http://i.imgur.com/hyvLK8j.png"><img src="http://i.imgur.com/lTXs3m4.png"><br>
          <strong>Fazer as ligações</strong><br><img src="http://i.imgur.com/LZHurKS.png">          
        </p>
        <a href="http://a.pomf.se/qjcfoq.webm">Efeito final</a>
        <p>
          <strong>Toggleable Light</strong><span class="text-muted"> A luz está nas Actor Classes do Content Browser</span><br><img src="http://i.imgur.com/5GuxjQn.png"><br>
          <a href="http://a.pomf.se/kalmkz.webm">Escrever na tela (Clique para vídeo do resultado)</a><br><img src="http://i.imgur.com/pfn2YvI.png">
        </p>
      </div>
    </div>
  </div>
</div>

  <h4>Lembretes:</h4>
  Meshes:
  <ul>
    <li>Static Mesh: parado</li>
    <li>RigidBody: sofre colisões</li>
    <li>InterpActor: Para animações com o Matinee</li>
  </ul>