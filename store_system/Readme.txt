How to Use
1. House Store: Create i_deed_vendor_house
2. Market Store
- Add store_system/ folder to spheretables.scp. Before the maps are loaded
- This system needs a roomdef with the following tags.

	[ROOMDEF a_market_store_1]
	EVENTS=r_default,r_no_pvp
	NAME=Store 1
	FLAGS=region_antimagic_all|region_antimagic_recall_in|region_antimagic_recall_out|region_antimagic_gate|region_flag_nobuilding|region_flag_insta_logout|region_flag_safe|region_flag_no_pvp|region_flag_nomining
	GROUP=Market
	P=5683,1107,5,10
	RECT=5681,1105,5687,1112,10
	tag.default_name=Store 1	// name will reset to this
	tag.max_vendors=2			// 2 or 4, other values will require dialog tweaking
	tag.max_lockdown=10
	tag.max_secure=1
	tag.store_price=500000

- Add region event in regiontypes.scp

	[REGIONTYPE r_store_system]
	ON=@Exit
		if <src.isplayer>
			src.dialogclose d_store_buy
			src.dialogclose d_store_owner
			src.dialogclose d_store_sell
			src.dialogclose d_store_vendor_skin
		endif

- Add speech event in spk_player.scp
	// [SPEECH spk_player]
	ON=*lock this*
		if <room> && <room.isevent.r_store_system>
			ref1=<room.tag.store_sign>
			if (!<isempty <account.tag.store>> && <account.tag.store>==<ref1>) || <isgm>
				targetf store_lockdown
				message @52,,1 What do you wish to lockdown?
			endif
		endif

	ON=*secure this*
		if <room> && <room.isevent.r_store_system>
			ref1=<room.tag.store_sign>
			if (!<isempty <account.tag.store>> && <account.tag.store>==<ref1>) || <isgm>
				targetf store_secure
				sysmessage @52,,1 What do you wish to secure?
			endif
		endif

	ON=*release this*
		if <room> && <room.isevent.r_store_system>
			ref1=<room.tag.store_sign>
			if (!<isempty <account.tag.store>> && <account.tag.store>==<ref1>) || <isgm>
				targetf store_release
				message @52,,1 What do you wish to release?
			endif
		endif
// **********************************
// all values

PLAYER
account.tag.store   // sign uid

SIGN
tag.store_owner		// owner uid
tag.gold            // earnings
tag.recycle_pack    // created inside store sign, when removing human w/equip items go here
tag.vendor_pack_#   // created inside layer_auction, holder for each vendor items
tag.vendor_#        // holds current vendor pack uid
tag.skin_#          // skin #
tag0.max_skins      // set on create as 3, max 10  (for more dialog needs rework)
tag.skins_used      // skin amount atm
tag.builton         // store creation date, added at store purchase
tag0.vendors        // N of active vendors
tag0.lockdowns      // current lockdowns
tag0.secure			// current secured containers
tag.dir				// for sign dispid dialog. 1 = facing west, empty = North 


PACKS (1 per vendor + 1 recycle)
tag.item_#          // uid of item for sale
tag.item_#_price    // price
tag.item_#_desc     // item description
tag0.skin_current    // skin used atm
tag.vendor_desc     // vendor short description on tooltip

VENDORS
tag.vendor_pack		// pack uid
tag0.pack			// pack #
tag.store_sign		// sign uid

ROOM
EVENTS=r_store_system        // used in store speech events
tag.default_name			 // used at store reset/remove
tag.store_sign				 // sign uid, used in store speech events
tag0.max_vendors             // max_vendors     // 2 - 4 // do not modify, dialog will break
tag0.max_secure              // max_secure, can be modified at areadef
tag0.store_price             // can be modified at areadef
tag.max_lockdown             // can be modified at areadef

@ClientTooltip
1042971     	// ~1_NOTHING~
1070722     	// ~1_NOTHING~
1114778			// ~1_val~
1114779			// ~1_val~
1060658-1060663	// ~1_val~: ~2_val~
1061640			// Owner: ~1_name~
1002034			// Arms Lore
1043304			// Price: ~1_COST~
1115224			// The amount of gold you are carrying.
1061829			// Relocate
1061156			// Gold
1071988			// Collect Gold
3000153			// Gold Avail
1114773		// ~1_VAL~<br>~2_VAL~
1114774		// ~1_VAL~<br>~2_VAL~<br>~3_VAL~
1011397		// Held Items

@ContextMenuRequest
src.addcontextentry 664,3002132,020,055	// Configuration (GM Menu)
src.addcontextentry 666,1150328,020,055	// OWNER MENU
src.addcontextentry 669,1078184,020,055	// Equip Items
src.addcontextentry 670,1078185,020,055	// Unequip Items
src.addcontextentry 681,3006103,020,055	// Buy
src.addcontextentry 682,3006104,020,055	// Sell
src.addcontextentry 683,1061829,020,055	// Relocate