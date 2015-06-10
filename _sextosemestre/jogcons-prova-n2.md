---
layout: artigo
nome: Jogos Para Consoles - 2015 S1 - Prova N2
---

Prova do primeiro semestre de 2015:

[2.0] Desenhe um par de hélices na tela (Dica apenas: utilize Math.PI para posicionar segunda base frente (180º) a primeira).

[3.0] Quadruplique a hélice inicial com cópias empilhadas um acima da outra, aqui não é necessário que tenham alinhamento ou aproximação ainda.

[1.0] Aproxime e alinhe as hélices para que se assemelhe a estrutura contínua do DNA.

[1.0] Utilizando apenas laços (ex: for, while), multiple no mínimo 12 vezes a helice inicial de forma que gere a estrutura contínua.

[1.0] Modifique seu código para a câmera ROTACIONE ao redor da estrutura PARADA, coforme exemplo.

[0.8] Modifique o código para que as teclas para frente e para trás aproxime ou distâncie a câmera que rotaciona ao redor da estrutura.

[1.2] Modifique o código para que cada base da hélice (elemento do par), tenha uma variação de ao menos quatro cores (as cores podem repetir)


(Código com a resposta só amanhã, espertinho(a))

<!--
{% highlight csharp %}

using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.GamerServices;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using Microsoft.Xna.Framework.Media;

namespace WindowsGame1
{
    /// <summary>
    /// This is the main type for your game
    /// </summary>
    public class Game1 : Microsoft.Xna.Framework.Game
    {
        GraphicsDeviceManager graphics;
        SpriteBatch spriteBatch;
        Model modeloDNA;
        Vector3 posicaoCamera, targetCamera;
        Matrix view;
        SpriteFont fonte;
        float multiplicadorHelixX = 62.0f, multiplicadorHelixY = 44.8f;
        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content";
        }

        /// <summary>
        /// Allows the game to perform any initialization it needs to before starting to run.
        /// This is where it can query for any required services and load any non-graphic
        /// related content.  Calling base.Initialize will enumerate through any components
        /// and initialize them as well.
        /// </summary>
        protected override void Initialize()
        {
            // TODO: Add your initialization logic here

            base.Initialize();

            posicaoCamera = new Vector3(0, -150, 150);
            targetCamera = new Vector3(0, 300, 0);
            view = Matrix.CreateLookAt(posicaoCamera, targetCamera, Vector3.Up);
        }

        /// <summary>
        /// LoadContent will be called once per game and is the place to load
        /// all of your content.
        /// </summary>
        protected override void LoadContent()
        {
            // Create a new SpriteBatch, which can be used to draw textures.
            spriteBatch = new SpriteBatch(GraphicsDevice);
            modeloDNA = Content.Load<Model>("ColunaxBase");
            fonte = Content.Load<SpriteFont>("fonte");
            // TODO: use this.Content to load your game content here
        }

        /// <summary>
        /// UnloadContent will be called once per game and is the place to unload
        /// all content.
        /// </summary>
        protected override void UnloadContent()
        {
            // TODO: Unload any non ContentManager content here
        }

        /// <summary>
        /// Allows the game to run logic such as updating the world,
        /// checking for collisions, gathering input, and playing audio.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Update(GameTime gameTime)
        {
            // Allows the game to exit
            if (GamePad.GetState(PlayerIndex.One).Buttons.Back == ButtonState.Pressed)
                this.Exit();

            ControlaCamera();
            // TODO: Add your update logic here

            base.Update(gameTime);
        }

        
        
        
        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.Black);


            float fov = MathHelper.ToRadians(45);

            Matrix world = Matrix.CreateRotationZ(0) * Matrix.CreateRotationX((float)Math.PI / 2);
            Matrix proj = Matrix.CreatePerspectiveFieldOfView(fov, GraphicsDevice.Viewport.AspectRatio, 1, 1000);

            /*
            [2.0] Desenhe um par de hélices na tela (Dica apenas: utilize Math.PI para posicionar segunda base frente (180º) a primeira).
            [3.0] Quadruplique a hélice inicial com cópias empilhadas um acima da outra, aqui não é necessário que tenham alinhamento ou aproximação ainda.
            [1.0] Utilizando apenas laços (ex: for, while), multiple no mínimo 12 vezes a helice inicial de forma que gere a estrutura contínua.
             */

            // Perdoem-me pelo posicionamento horrível das hélices
            int quantidadeHelices = 12;
            int colorIndex = 0;
            for (int i = 0; i < quantidadeHelices; i++)
            {
                for (int j = 0; j < 2; j++)
                {
                    if (colorIndex % 2 == 0)
                    {
                        world = Matrix.CreateRotationZ(i*multiplicadorHelixX) * Matrix.CreateRotationX((float)Math.PI / 2) * Matrix.CreateTranslation(new Vector3(0, i * 28, 0));
                    }
                    else
                    {
                        world = Matrix.CreateRotationZ(-i*multiplicadorHelixY) * Matrix.CreateRotationZ(MathHelper.ToRadians(180)) * Matrix.CreateRotationX((float)Math.PI / 2) * Matrix.CreateTranslation(new Vector3(0, i * 28, 0));
                    }
                    foreach (var mesh in modeloDNA.Meshes)
                    {
                        foreach (BasicEffect be in mesh.Effects)
                        {
                            be.World = world;
                            be.Projection = proj;
                            be.View = view;
                            be.EnableDefaultLighting();
                            // [1.2] Modifique o código para que cada base da hélice (elemento do par), tenha uma variação de ao menos quatro cores (as cores podem repetir)
                            if (colorIndex == 0) be.DiffuseColor = Color.Wheat.ToVector3();      // W
                            if (colorIndex == 1) be.DiffuseColor = Color.IndianRed.ToVector3();  // I
                            if (colorIndex == 2) be.DiffuseColor = Color.Lavender.ToVector3();   // L
                            if (colorIndex == 3) be.DiffuseColor = Color.LawnGreen.ToVector3();  // L
                        }
                        if (colorIndex >= 3) { colorIndex = 0; } else { colorIndex++; } //Reinicia e controla a cor
                        mesh.Draw();
                    }
                }
            }

            /*
            spriteBatch.Begin();
            string debugString = "Debug\nmultiplicadorHelixX: " + multiplicadorHelixX.ToString() + "\nmultiplicadorHelixY: " + multiplicadorHelixY.ToString();
            spriteBatch.DrawString(fonte, debugString, new Vector2(10,10), Color.White);
            spriteBatch.End();
            */

            base.Draw(gameTime);
        }

        void ControlaCamera()
        {
            
            KeyboardState ks = Keyboard.GetState();

            /* Debug
            if (ks.IsKeyDown(Keys.P)) multiplicadorHelixX += 0.1f;
            if (ks.IsKeyDown(Keys.K)) multiplicadorHelixY += 0.1f;
            if (ks.IsKeyDown(Keys.M)) multiplicadorHelixY -= 0.1f;
            */

            /* Código que eu escrevi antes de me tocar que era pra rotacionar ao redor
            if (ks.IsKeyDown(Keys.Left))
            {
                posicaoCamera.X--;
                targetCamera.X--;
            }

            if (ks.IsKeyDown(Keys.Right))
            {
                posicaoCamera.X++;
                targetCamera.X++;
            }
            */


            // [0.8] Modifique o código para que as teclas para frente e para trás aproxime ou distâncie a câmera que rotaciona ao redor da estrutura.

                if (ks.IsKeyDown(Keys.Down)){
                    posicaoCamera.Z--;
                    posicaoCamera.Y--;
                }
                if (ks.IsKeyDown(Keys.Up)){
                    posicaoCamera.Z++;
                    posicaoCamera.Y++;
                }


            // [1.0] Modifique seu código para a câmera ROTACIONE ao redor da estrutura PARADA, coforme exemplo.
            posicaoCamera = Vector3.Transform(posicaoCamera, Matrix.CreateRotationY(0.01f));
            view = Matrix.CreateLookAt(posicaoCamera, targetCamera, Vector3.Up);       
        }
    }
}


{% endhighlight %}

-->
