---
layout: artigo
nome: Jogos Para Consoles - Nave com XNA Simples
---

# Nave com XNA Simples
<span class="text-muted">Basicamente uma versão "limpa" do simulado que ele deu</span>

### Game1.cs

{% highlight csharp %}
#region File Description
//-----------------------------------------------------------------------------
// MonoEstudoGame.cs
//
// Microsoft XNA Community Game Platform
// Copyright (C) Microsoft Corporation. All rights reserved.
//-----------------------------------------------------------------------------
#endregion

#region Using Statements
using System;

using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using Microsoft.Xna.Framework.Input.Touch;
using Microsoft.Xna.Framework.Storage;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.Media;

#endregion

namespace MonoEstudo
{
	/// <summary>
	/// Default Project Template
	/// </summary>
	public class Game1 : Game
	{

		#region Fields

		GraphicsDeviceManager graphics;
		SpriteBatch spriteBatch;
		Nave nave;
		FundoInfinito fundoInfinito1, fundoInfinito2, fundoInfinito3;

		#endregion

		#region Initialization

		public Game1 ()
		{
			graphics = new GraphicsDeviceManager (this);
			
			Content.RootDirectory = "Assets"; //Pasta padrão do MonoGame, não copia isso aqui não, gênio

			graphics.IsFullScreen = false;
		}

		/// <summary>
		/// Overridden from the base Game.Initialize. Once the GraphicsDevice is setup,
		/// we'll use the viewport to initialize some values.
		/// </summary>
		protected override void Initialize ()
		{
			base.Initialize ();
		}


		/// <summary>
		/// Load your graphics content.
		/// </summary>
		protected override void LoadContent ()
		{
			// Create a new SpriteBatch, which can be use to draw textures.
			spriteBatch = new SpriteBatch (graphics.GraphicsDevice);
			
			// Inicializando a nave
			nave = new Nave(Content);

			// Inicializando os fundos
			fundoInfinito1 = new FundoInfinito (Content, "fundo", 5);
			fundoInfinito2 = new FundoInfinito (Content, "nebula1", 10); 
			fundoInfinito3 = new FundoInfinito (Content, "nebula2", 15);
		}

		#endregion

		#region Update and Draw

		/// <summary>
		/// Allows the game to run logic such as updating the world,
		/// checking for collisions, gathering input, and playing audio.
		/// </summary>
		/// <param name="gameTime">Provides a snapshot of timing values.</param>
		protected override void Update (GameTime gameTime)
		{
			// TODO: Add your update logic here			
            		
			base.Update (gameTime);
		}

		/// <summary>
		/// This is called when the game should draw itself. 
		/// </summary>
		/// <param name="gameTime">Provides a snapshot of timing values.</param>
		protected override void Draw (GameTime gameTime)
		{
			// Clear the backbuffer
			graphics.GraphicsDevice.Clear (Color.CornflowerBlue);

			spriteBatch.Begin ();
			fundoInfinito1.Desenhar (spriteBatch);
			fundoInfinito2.Desenhar (spriteBatch);
			fundoInfinito3.Desenhar (spriteBatch);
			// desenha a nave (Sim, ela tem que receber o spriteBatch)
			nave.Desenhar(spriteBatch);

			spriteBatch.End ();

			base.Draw (gameTime);
		}

		#endregion
	}
}


{% endhighlight %}


### Nave.cs

{% highlight csharp %}
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using Microsoft.Xna.Framework.Content;


namespace MonoEstudo
{
	class Nave
	{
		readonly Texture2D sprite;
		Vector2 posicao = new Vector2(50,50);
		const float spd = 10.0f, desaceleracao = 0.02f;
		float velocidadeX, velocidadeY;
		Rectangle recorteNave;
		int larguraSpriteNave, alturaSpriteNave, spriteHIndex, spriteWIndex;

		/// <summary>
		/// Inicializa a nave. Deve receber um ContentManager.
		/// </summary>
		public Nave(ContentManager Content){
			sprite = Content.Load<Texture2D> ("naveSprite");
			velocidadeX = velocidadeY = 0.0f;
		}

		#region Movimentação

		/// <summary>
		/// Lida com a movimentação da nave. É chamada a cada atualização de status.
		/// </summary>
		void Movimentar(){
			
			//Inputs do teclado
			if(Keyboard.GetState().IsKeyDown(Keys.Up)){
				Sobe();
			}
			if(Keyboard.GetState().IsKeyDown(Keys.Down)){
				Desce();
			}
			if(Keyboard.GetState().IsKeyDown(Keys.Right)){
				Acelera();
			}
			if(Keyboard.GetState().IsKeyDown(Keys.Left)){
				Freia();
			}

			//Atualiza posição
			posicao.X += velocidadeX;
			posicao.Y += velocidadeY;
		}

		void Sobe(){
			velocidadeY -= spd;
			spriteHIndex = 0;
		}
		void Desce(){
			velocidadeY += spd;
			spriteHIndex = 2;
		}
		void Acelera(){
			velocidadeX += spd;
			spriteWIndex = 2;
		}
		void Freia(){
			velocidadeX -= spd;
			spriteWIndex = 1;
		}

		/// <summary>
		/// Não funciona
		/// </summary>
		void Desacelerar(){
			velocidadeX *= desaceleracao;
			velocidadeY *= desaceleracao;
		}

		#endregion

		/// <summary>
		/// Atualiza o status geral da nave. É chamada sempre antes de se desenhar a nave.
		/// </summary>
		void Atualizar(){
			//Reseta valores
			alturaSpriteNave = 39;
			larguraSpriteNave = 41;
			spriteHIndex = 1;
			spriteWIndex = 0;

			Desacelerar ();
			Movimentar ();

			recorteNave = new Rectangle (larguraSpriteNave * spriteWIndex, alturaSpriteNave * spriteHIndex, 45, 39);
		}

		/// <summary>
		/// Desenha a nave.
		/// </summary>
		public void Desenhar(SpriteBatch sp){
			Atualizar ();
			sp.Draw (sprite, posicao, recorteNave, Color.White, 0, new Vector2 (23, 0), new Vector2 (1, 1), SpriteEffects.None, 0);
		}
	}
}
{% endhighlight %}




### FundoInfinito.cs

{% highlight csharp %}
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoEstudo
{
	class FundoInfinito
	{
		Texture2D sprite;
		int posicao;
		int velocidade;

		/// <summary>
		/// Inicializa o FundoInfinito
		/// </summary>
		/// <param name="assetName">Nome do asset do fundo</param>
		/// <param name="speed">Velocidade do fundo</param>
		public FundoInfinito(ContentManager Content, string assetName, int speed){
			sprite = Content.Load<Texture2D> (assetName);
			velocidade = Math.Abs(speed); //To sem paciência de fazer ele ir pro outro lado
		}

		void Atualizar(){
			//movimentação do fundo
			posicao -= velocidade;

			//calcula a repetição do fundo
			if (posicao + sprite.Width <= 0) {
				posicao = 0;
			}
		}

		/// <summary>
		/// Desenha os fundos de forma infinita.
		/// </summary>
		public void Desenhar(SpriteBatch sp){
			Atualizar ();
			sp.Draw (sprite, new Vector2 (posicao, 0), Color.White);
			sp.Draw (sprite, new Vector2 (posicao + sprite.Width - 2, 0), Color.White); // -2 pra sumir aquela barra branca chata
		}
	}
}
{% endhighlight %}