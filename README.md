//Mob-Arena-2
//Mod for mob arena


import org.bukkit.Location;
import org.bukkit.Material;
import org.bukkit.World;
import org.bukkit.entity.EntityType;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;
import org.bukkit.event.player.PlayerInteractEvent;
import org.bukkit.inventory.ItemStack;

public class MobArena implements Listener {

    private static final Material ARENA_ITEM = Material.NETHER_STAR;
    private static final int ARENA_SIZE = 50;
    private static final int SPAWN_DELAY = 20; // in ticks
    private static final int SPAWN_LIMIT = 100;

    private Location lobbyLocation;
    private Location arenaLocation;

    public MobArena(Location lobbyLocation, Location arenaLocation) {
        this.lobbyLocation = lobbyLocation;
        this.arenaLocation = arenaLocation;
    }

    @EventHandler
    public void onPlayerInteract(PlayerInteractEvent event) {
        Player player = event.getPlayer();
        ItemStack item = player.getInventory().getItemInMainHand();
        if (item.getType() == ARENA_ITEM) {
            player.teleport(lobbyLocation);
            player.sendMessage("Welcome to the mob arena! Right-click the sign to select your settings and start the game.");
        }
    }

    public void startGame(Player player, int waves, int difficulty, EntityType[] mobTypes) {
        World world = arenaLocation.getWorld();
        player.teleport(arenaLocation);
        player.sendMessage("The game has started! Get ready to battle " + waves + " waves of mobs at difficulty level " + difficulty + ".");
        int mobCount = 0;
        for (int i = 0; i < waves; i++) {
            for (EntityType mobType : mobTypes) {
                Location spawnLocation = getRandomLocation(arenaLocation, ARENA_SIZE);
                world.spawnEntity(spawnLocation, mobType);
                mobCount++;
                if (mobCount >= SPAWN_LIMIT) {
                    break;
                }
            }
            if (mobCount >= SPAWN_LIMIT) {
                break;
            }
            try {
                Thread.sleep(SPAWN_DELAY * 50); // convert ticks to milliseconds
            } catch (InterruptedException e) {
                // ignore
            }
        }
        player.sendMessage("The game is over! You defeated " + mobCount + " mobs.");
    }

    private Location getRandomLocation(Location center, int radius) {
        World world = center.getWorld();
        double x = center.getX() + (Math.random() - 0.5) * radius;
        double y = center.getY() + (Math.random() - 0.5) * radius;
        double z = center.getZ() + (Math.random() - 0.5) * radius;
        return new Location(world, x, y, z);
   
