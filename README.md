import wollok.game.*

object juego{
	var property zombies = []
	var property girasoles = []
	var property plantas = []
	var property soles = 25

  	method iniciar(){
		game.title("Plantas vs Zombies")
		game.width(30)
		game.height(20)
		game.boardGround("./img/fondo.png")
    	game.start()
    	self.mostrar_soles()
    	game.onTick(10000, "zombie aparece", { self.agregar_zombiecomun() })
    	game.onTick(30000, "zombie con casco aparece", { self.agregar_zombieconCasco() })
    	game.onTick(8000, "se genera sol", { girasoles.forEach{girasol=> self.generar_soles()} })
    	game.onTick(2000, "zombie se mueve", { zombies.forEach{zombie=> zombie.avanzar()} })
    	game.schedule(120000,{game.removeTickEvent("zombie aparece")})
    	game.schedule(120000,{game.removeTickEvent("zombie con casco aparece")})
    	
    	keyboard.enter().onPressDo { self.agregar_girasol() }
    	keyboard.space().onPressDo { self.agregar_nuez() }
    	keyboard.x().onPressDo { self.agregar_lanzaguisantes() }
    	
  	}

	method agregar_zombiecomun(){
  		const zombie = new Zombiecomun()
  		game.addVisual(zombie)
  		zombies.add(zombie)
  	} 
  
	method agregar_zombieconCasco(){
  		const zombieconcasco = new ZombieconCasco()
  		game.addVisual(zombieconcasco)
  		zombies.add(zombieconcasco)
  	} 
  
	method agregar_nuez(){
  		if(soles>=50){	
  			const nuez = new Nuez()
  			game.addVisual(nuez)
  			plantas.add(nuez)
  			soles = soles - 50
  		return true
  		}
  	return false
  	}  
  
	method agregar_girasol(){	
  		if(soles>=25){	
  			const girasol = new Girasol()
  			game.addVisual(girasol)
 			girasoles.add(girasol)
  			plantas.add(girasol)
  			soles = soles - 25
  		return true
  		}
  	return false
  	}
  	
  	method agregar_lanzaguisantes(){
		if(soles>=100){
			const lanzaguisantes = new Lanzaguisantes()
			game.addVisual(lanzaguisantes)
			plantas.add(lanzaguisantes)
			soles = soles - 100
		return true
		}
	return false
	}
  	
  
	method generar_soles(){
  		soles = soles + 25 
  	}
 
	method mostrar_soles(){
  		game.addVisual(cantidadSoles)
  		}
	}

class Plantas{
	var property durabilidad = 0
	var property position = game.at(3, 3.randomUpTo(15).truncate(0))
	var property nombre = 'planta'
	method perder_durabilidad(){
		durabilidad-=1
		if(durabilidad<=0){
			self.desaparecer()
		}
	}

	method desaparecer(){
		game.removeVisual(self)
	}
}

class Nuez inherits Plantas(durabilidad = 30, position = game.at(9, 3.randomUpTo(15).truncate(0))){
	method image() = "./img/nuez.png"	

}

class Girasol inherits Plantas(durabilidad = 15){
	var property soles = juego.soles()
	method image() = "./img/girasol.png"
 
}

class Lanzaguisantes inherits Plantas(durabilidad = 15, position = game.at(6, 3.randomUpTo(15).truncate(0))){
	method image() = "lanzaguisantes.png"

}

class Zombie {
	var property nombre = 'zombie'
	var property position = game.at(25, 4.randomUpTo(14).truncate(0))
	var property vida = 0
	var property colision = []

	method avanzar(){
		if (not(self.parar())){
			position = position.left(1)		
		}
  	}

	method parar(){
		var valor = 0
		game.allVisuals().forEach{visual =>
			if (visual.position() == position.left(1) and visual.nombre() == 'planta'){
				valor +=1
			visual.perder_durabilidad()
			} 
		}
	return valor > 0
	}
}



class Zombiecomun inherits Zombie(vida = 10){
	method image() = "./img/zombiecomun.png"

}
  
class ZombieconCasco inherits Zombie(vida = 15){	
	method image() = "./img/zombieconCasco.png"

}


object paleta {
	const property blanco = "FFFFFFFF"
	var property nombre = 'paleta'
}

object cantidadSoles {

	var property nombre = 'soles'
	method position() = game.at(3,18)
	
	method image() = "./img/sol.png"
	
	method text() = ("Tus soles: " + juego.soles())
	
method textColor() = paleta.blanco()
}
