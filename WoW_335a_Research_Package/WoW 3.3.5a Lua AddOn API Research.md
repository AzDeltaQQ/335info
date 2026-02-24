# WoW 3.3.5a Lua AddOn API Research

This document contains the findings of the research on the World of Warcraft 3.3.5a Lua AddOn API.


## Sources

* https://wowwiki-archive.fandom.com/wiki/World_of_Warcraft_API

## Global Functions

This is a list of global functions available in the WoW 3.3.5a API, compiled from the WoWWiki archive.

### Account

*   `GetAccountExpansionLevel()` - Returns registered expansion. (0=WoW, 1=BC, 2=WotLK, 3=Cata, 4=Mists, 5=Warlords, 6=Legion, 7=BfA, 8=Shadowlands)
*   `GetBillingTimeRested()` - returns the time spent logged in current billing unit.
*   `PartialPlayTime()` - returns 1 if the player is currently "tired": reduced XP, loot.
*   `NoPlayTime()` - returns 1 if the player is currently "unhealthy": no XP, loot.

### Achievement

*   `AddTrackedAchievement(achievementId)` - Add an achievement to tracking.
*   `CanShowAchievementUI()` - Returns if the AchievementUI can be displayed
*   `ClearAchievementComparisonUnit()` - Remove the unit being compared.
*   `GetAchievementCategory(achievementID)` - Return the category number of the requested achievement.
*   `GetAchievementComparisonInfo(achievementID, comparisonNum)` - Returns status of achievement for comparison player.
*   `GetAchievementCriteriaInfo(achievementID, criteriaIndex)` - Returns information about the requested criteria.
*   `GetAchievementCriteriaInfoByID(achievementID, criteriaID)` - Returns information about the requested criteria. (added 5.0.4)
*   `GetAchievementInfo(achievementID)` or (category, offset) - Returns information about the requested Achievement.
*   `GetAchievementInfoFromCriteria(id)` - Returns information about the requested Achievement.
*   `GetAchievementLink(achievementID)` - Returns a achievementLink for the specified Achievement.
*   `GetAchievementNumCriteria(achievementID)` - Return the number of criteria the requested Achievement has.
*   `GetAchievementNumRewards(achievementID)` - Return the number of rewards the requested Achievement has.
*   `GetCategoryInfo(category)` - Return information about the requested category
*   `GetCategoryList()` - Returns the list of Achievement categories.
*   `GetCategoryNumAchievements(category)` - Return the total Achievements and number completed for the specific category.
*   `GetComparisonAchievementPoints()` - Return the total number of achievement points the comparison unit has earned.
*   `GetComparisonCategoryNumAchievements(achievementID)`
*   `GetComparisonStatistic(achievementID)` - Return the value of the requested statistic for the comparison player.
*   `GetLatestCompletedAchievements()` - Return the ID's of the last 5 completed Achievements.
*   `GetLatestCompletedComparisonAchievements()`
*   `GetLatestUpdatedComparisonStats()`
*   `GetLatestUpdatedStats()` - Return the ID's of the last 5 updated Statistics.
*   `GetNextAchievement(achievementID)`
*   `GetNumComparisonCompletedAchievements()`
*   `GetNumCompletedAchievements([guildOnly])` - Returns total and completed number of achievements, or only guild.
*   `GetPreviousAchievement(achievementID)` - Return previous related achievements.
*   `GetStatistic(achievementID)` - Return the value of the requested statistic.
*   `GetStatisticsCategoryList()` - Returns the list of Statistic categories.
*   `GetTotalAchievementPoints([guildOnly])` - Return the total, or only guild, achievement points earned.
*   `GetTrackedAchievements()` - Return the AchievementID of the currently tracked achievements
*   `GetNumTrackedAchievements()` - Return the total number of the currently tracked achievements
*   `RemoveTrackedAchievement(achievementID)` - Stops an achievement from being tracked
*   `SetAchievementComparisonUnit(unitId)` - Set the unit to be compared to.

### Action

*   `ActionButtonDown(id)` - Press the specified action button. (protected 2.0)
*   `ActionButtonUp(id)` - Release the specified action button. (protected 2.0)
*   `ActionHasRange(slot)` - Determine if the specified action is a range restriction [1 if yes, nil if no]
*   `BonusActionButtonDown()` - Trigger the specified bonus(pet or minion) action button.
*   `BonusActionButtonUp()` - Release the specified bonus(pet or minion) action button.
*   `CameraOrSelectOrMoveStart()` - Begin "Left click" in the 3D world. (protected 1.10)
*   `CameraOrSelectOrMoveStop([stickyFlag])` - End "Left click" in the 3D world. (protected 1.10)
*   `ChangeActionBarPage(page)` - Changes the current action bar page.
*   `GetActionBarPage()` - Return the current action bar page. CURRENT_ACTIONBAR_PAGE is obsolete.
*   `GetActionBarToggles()` - Return the toggles for each action bar.
*   `GetActionCooldown(slot)` - This returns the cooldown values of the specified action..
*   `GetActionCount(slot)` - Get the count (bandage/potion/etc) for an action, returns 0 if none or not applicable.
*   `GetActionInfo(slot)` - Returns type, id, subtype.
*   `GetActionText(slot)` - Get the text label (macros, etc) for an action, returns nil if none.
*   `GetActionTexture(slot)` - Gets the texture path for the specified action.
*   `GetBonusBarOffset()` - Determine which page of bonus actions to show.
*   `GetMouseButtonClicked()` - Returns the name of the button that triggered a mouse down/up/click/doubleclick event. (added 2.0.3)
*   `GetMultiCastBarOffset()` - Returns the page offset of the multicast action IDs (added 3.2)
*   `GetPossessInfo(index)` - Returns texture, name, enabled.
*   `HasAction(slot)` - Returns 1 if the player has an action in the specified slot, nil otherwise.
*   `IsActionInRange(slot,[unit])` - Test if an action is in range (1=yes, 0=no, nil=not applicable).
*   `IsAttackAction(slot)` - Return 1 if an action is an 'attack' action (flashes during combat), nil otherwise.
*   `IsAutoRepeatAction(slot)` - Return 1 if an action is auto-repeating, nil otherwise.
*   `IsCurrentAction(slot)` - Return 1 if an action is the one currently underway, nil otherwise.
*   `IsConsumableAction(slot)` - Return 1 if an action is consumable (i.e. has a count), nil otherwise.
*   `IsEquippedAction(slot)` - Return 1 if an action is equipped (i.e. connected to an item that must be equipped), nil otherwise.
*   `IsUsableAction(slot)` - Return 1 if an action can be used at present, nil otherwise.
*   `PetHasActionBar()` - Determine if player has a pet with an action bar.
*   `PickupAction(slot)` - Drags an action out of the specified quickbar slot and holds it on the cursor.
*   `PickupPetAction(slot)` - Drags an action from the specified pet action bar slot into the cursor.
*   `PlaceAction(slot)` - Drops an action from the cursor into the specified quickbar slot.
*   `SetActionBarToggles(show1,show2,show3,show4[, alwaysShow])` - Set show toggle for each action bar - 'alwaysShow' (added 1.12)
*   `StopAttack()` - Turns off auto-attack, if currently active. Has no effect is the player does not currently have auto-attack active.
*   `TurnOrActionStart()` - Begin "Right Click" in the 3D world. (protected 1.10)
*   `TurnOrActionStop()` - End "Right Click" in the 3D world. (protected 1.10)

### Calendar

*   `CalendarAddEvent()` - Creates and selects a new event and adds it to the calendar
*   `CalendarCanAddEvent()` - Returns true if the player can add an event to the calendar
*   `CalendarCanSendInvite()` - Returns true if the player can send an invitation to an event
*   `CalendarCloseEvent()` - Closes the event details frame
*   `CalendarContextDeselectEvent()`
*   `CalendarContextEventShouldShowRemove()`
*   `CalendarContextEventShouldShowStatus()`
*   `CalendarContextGetEventIndex()`
*   `CalendarContextGetEventInfo()`
*   `CalendarContextInviteAvailable()`
*   `CalendarContextInviteDecline()`
*   `CalendarContextInviteRemove()`
*   `CalendarContextInviteStatus()`
*   `CalendarContextIsEventOpen()`
*   `CalendarContextRemoveEvent()`
*   `CalendarContextSelectEvent()`
*   `CalendarDefaultGuildFilter()`
*   `CalendarEventAvailable()`
*   `CalendarEventCanEdit()`
*   `CalendarEventCanModerate()`
*   `CalendarEventClearAutoApprove()`
*   `CalendarEventClearLocked()`
*   `CalendarEventClearModerator()`
*   `CalendarEventComplain()`
*   `CalendarEventDecline()`
*   `CalendarEventGetCalendarType()`
*   `CalendarEventGetInviteInfo(inviteIndex)`
*   `CalendarEventGetInviteSortCriterion()`
*   `CalendarEventGetSelectedInvite()`
*   `CalendarEventGetStatusOptions()`
*   `CalendarEventGetTextures()`
*   `CalendarEventGetTitle()`
*   `CalendarEventHasPendingInvite()`
*   `CalendarEventHaveSettingsChanged()`
*   `CalendarEventInProgress()`
*   `CalendarEventIsModerator()`
*   `CalendarEventIsSelected()`
*   `CalendarEventRemoveInvite(inviteIndex)`
*   `CalendarEventSelectInvite(inviteIndex)`
*   `CalendarEventSetAutoApprove()`
*   `CalendarEventSetDate(month, day, year)`
*   `CalendarEventSetDescription(description)`
*   `CalendarEventSetLocked()`
*   `CalendarEventSetLockoutDate(lockoutDate)`
*   `CalendarEventSetLockoutTime(lockoutTime)`
*   `CalendarEventSetModerator(index)`
*   `CalendarEventSetRepeatOption(repeatoption)`
*   `CalendarEventSetSize`
*   `CalendarEventSetStatus(index, status)` - Sets the invitation status of a player to the current event
*   `CalendarEventSetTextureID(textureIndex)`
*   `CalendarEventSetTime(hour, minute)`
*   `CalendarEventSetTitle(title)`
*   `CalendarEventSetType(type)`
*   `CalendarEventSignUp()`
*   `CalendarEventSortInvites(criterion)`
*   `CalendarEventTentative()`
*   `CalendarGetAbsMonth()` - returns month, year
*   `CalendarGetDate()` - Call this only after PLAYER_ENTERING_WORLD event
*   `CalendarGetDayEvent(monthOffset, day, eventIndex)`
*   `CalendarGetDayEventSequenceInfo` - Retrieve information about the specified event.
*   `CalendarGetEventIndex()` - returns monthOffset, day, index
*   `CalendarGetEventInfo()` - Returns detailed information about an event selected with CalendarOpenEvent()
*   `CalendarGetFirstPendingInvite(monthOffset, day)` - returns eventIndex
*   `CalendarGetHolidayInfo(monthOffset, day, eventIndex)` - Returns Holiday Name, Holiday Description, Calendar Icon
*   `CalendarGetMaxCreateDate()` - returns maxWeekday, maxMonth, maxDay, maxYear
*   `CalendarGetMaxDate()` - returns maxWeekday, maxMonth, maxDay, maxYear
*   `CalendarGetMinDate()` - returns minWeekday, minMonth, minDay, minYear
*   `CalendarGetMinHistoryDate()` - returns minWeekday, minMonth, minDay, minYear
*   `CalendarGetMonth([monthOffset])` - returns month, year
*   `CalendarGetMonthNames()` - returns a list of the month names
*   `CalendarGetNumDayEvents(monthOffset[, day])`
*   `CalendarGetNumPendingInvites()` - returns count
*   `CalendarGetRaidInfo (monthOffset, day, eventIndex)` - returns name, calendarType, raidID, hour, minute, difficulty
*   `CalendarGetWeekdayNames()` - returns a list of the weekday names
*   `CalendarIsActionPending()` - returns isPending
*   `CalendarMassInviteArenaTeam(teamType)`
*   `CalendarMassInviteGuild(minLevel, maxLevel, rank)`
*   `CalendarNewEvent()` - Creates and selected a new event
*   `CalendarNewGuildAnnouncement()`
*   `CalendarNewGuildEvent(minLevel, maxLevel, minRank)` - Replaces the invite list of the selected new event with the specified guild members
*   `CalendarOpenEvent(monthOffset, day, eventIndex)` - Selects an existing event
*   `CalendarRemoveEvent()` - Removes the selected event from the calendar (invitees only)
*   `CalendarSetAbsMonth(month, year)` - Sets the reference month and year for functions which use a month offset
*   `CalendarSetMonth(monthOffset)`
*   `CalendarUpdateEvent()` - Saves the selected event (existing events only, requires hardware input to call)
*   `OpenCalendar()` - Requests calendar information from the server. Does not open the calendar frame. (added 3.0.8)

### Camera

*   `CameraOrSelectOrMoveStart()` - Begin "Left click" in the 3D world. (protected 1.10)
*   `CameraOrSelectOrMoveStop([stickyFlag])` - End "Left click" in the 3D world. (protected 1.10)
*   `CameraZoomIn(increment)` - Zooms the camera into the viewplane by increment.
*   `CameraZoomOut(increment)` - Zooms the camera out of the viewplane by increment.
*   `FlipCameraYaw(degrees)` - Rotates the camera about the Z-axis by the angle amount specified in degrees.
*   `IsMouselooking()` - Returns 1 if mouselook is currently active, nil otherwise.
*   `MouselookStart()` - Enters mouse look mode; mouse movement is used to adjust movement/facing direction.
*   `MouselookStop()` - Exits mouse look mode; mouse movement is used to move the mouse cursor.
*   `MoveViewDownStart()` - Begins rotating the camera downward.
*   `MoveViewDownStop()` - Stops rotating the camera after #MoveViewDownStart() is called.
*   `MoveViewInStart()` - Begins zooming the camera in.
*   `MoveViewInStop()` - Stops zooming the camera in after #MoveViewInStart() is called.
*   `MoveViewLeftStart()` - Begins rotating the camera to the Left.
*   `MoveViewLeftStop()` - Stops rotating the camera after #MoveViewLeftStart() is called.
*   `MoveViewOutStart()` - Begins zooming the camera out.
*   `MoveViewOutStop()` - Stops zooming the camera out after #MoveViewOutStart() is called.
*   `MoveViewRightStart()` - Begins rotating the camera to the Right.
*   `MoveViewRightStop()` - Stops rotating the camera after #MoveViewRightStart() is called.
*   `MoveViewUpStart()` - Begins rotating the camera upward.
*   `MoveViewUpStop()` - Stops rotating the camera after #MoveViewUpStart() is called.
*   `PitchDownStart()` - Begins pitching the camera Downward.
*   `PitchDownStop()` - Stops pitching the camera after #PitchDownStart() is called.
*   `PitchUpStart()` - Begins pitching the camera Upward.
*   `PitchUpStop()` - Stops pitching the camera after #PitchUpStart() is called.
*   `NextView()` - Cycles forward through the five predefined camera positions.
*   `PrevView()` - Cycles backward through the five predefined camera positions.
*   `ResetView(index)` - Resets the specified (1-5) predefined camera position to its default if it was changed using #SaveView(index).
*   `SaveView(index)` - Replaces the specified (1-5) predefined camera positions with the current camera position.
*   `SetView(index)` - Sets camera position to a specified (1-5) predefined camera position.

### Channel

*   `AddChatWindowChannel(chatFrameIndex, "channel")` - Make a chat channel visible in a specific ChatFrame.
*   `ChannelBan("channel", "name")` - Bans a player from the specified channel.
*   `ChannelInvite("channel", "name")` - Invites the specified user to the channel.
*   `ChannelKick("channel", "name")` - Kicks the specified user from the channel.
*   `ChannelModerator("channel", "name")` - Sets the specified player as the channel moderator.
*   `ChannelMute("channel", "name")` - Turns off the specified player's ability to speak in a channel.
*   `ChannelToggleAnnouncements("channel")` - Toggles the channel to display announcements either on or off.
*   `ChannelUnban("channel", "name")` - Unbans a player from a channel.
*   `ChannelUnmoderator("channel", "name")` - Takes the specified user away from the moderator status.
*   `ChannelUnmute("channel", "name")` - Unmutes the specified user from the channel.
*   `DisplayChannelOwner("channel")` - Displays the owner of the specified channel in the default chat.
*   `DeclineInvite("channel")` - Declines an invitation to join a chat channel.
*   `EnumerateServerChannels()` - Retrieves all available server channels (zone dependent).
*   `GetChannelList()` - Retrieves joined channels.
*   `GetChannelName("channel" or index)` - Retrieves the name from a specific channel.
*   `GetChatWindowChannels(index)` - Get the chat channels received by a chat window.
*   `JoinChannelByName("channel"[, "password"[, frameId]])` - Join the specified chat channel, with optional password, and register for specified frame. (updated 1.9)
*   `LeaveChannelByName("channel")` - Leaves the channel with the specified name.
*   `ListChannelByName(channelMatch)` - Lists members in the given channel to the chat window.
*   `ListChannels()` - Lists all of the channels into the chat window.
*   `RemoveChatWindowChannel(chatFrameIndex, "channel")` - Make a chat channel invisible (hidden) in a specific ChatFrame.
*   `SendChatMessage("msg",[ "chatType",[ "language",[ "channel"]]])` - Sends a chat message.
*   `SetChannelOwner("channel", "name")` - Sets the channel owner.
*   `SetChannelPassword("channel", "password")` - Changes the password of the current channel.

### Character

*   `AcceptResurrect()` - The player accepts the request from another player to resurrect him/herself.
*   `AcceptXPLoss()` - Accept the durability loss to be reborn by a spirit healer. The name is a remnant from when spirit res was an XP loss instead.
*   `CheckBinderDist()` - Check whether the player is close enough to interact with the Hearthstone binder.
*   `ConfirmBinder()` - Confirm the request to set the binding of the player's Hearthstone.
*   `DeclineResurrect()` - Decline the request from another player to resurrect him/herself.
*   `DestroyTotem(slot)`
*   `GetBindLocation()` - Get the name of the location for your Hearthstone.
*   `GetComboPoints()` - Get the current number of combo points.
*   `GetCorpseRecoveryDelay()` - Time left before a player can accept a resurrection
*   `GetCurrentTitle()` - Returns the player's current titleId.
*   `GetMirrorTimerInfo(id)` - returns information about a mirror timer (exhaustion, breath and feign death timers)
*   `GetMirrorTimerProgress(id)` - returns the current value of a mirror timer (exhaustion, breath and feign death timers)
*   `GetMoney()` - Returns an integer value of your held money in copper.
*   `GetNumTitles()` - Returns the maximum titleId
*   `GetPlayerFacing()` - Returns the direction the player is facing in radians ([-R, R] range, 0 is north, R/2 is east). (added 3.1)
*   `GetPVPDesired()` - Returns whether the player has permanently turned on their PvP flag.
*   `GetReleaseTimeRemaining()` - Returns the amount of time left before your ghost is pulled from your body.
*   `GetResSicknessDuration()`
*   `GetRestState()` - Returns information about a player's rest state (saved up experience bonus)
*   `GetRuneCooldown(id)` - Returns cooldown information about a given rune.
*   `GetRuneCount(slot)`
*   `GetRuneType(id)` - Returns the type of rune with the given id.
*   `GetTimeToWellRested()` - Defunct.
*   `GetTitleName(titleId)` - Returns the player's current title name
*   `GetUnitPitch(UnitID)` - Returns the true pitch radians ([-R, R] 0 is level). UnitID restricted to "player".
*   `GetXPExhaustion()` - Returns your character's current rested XP, nil if character is not rested.
*   `HasFullControl()`
*   `HasSoulstone()`
*   `IsFalling()` - Returns 1 if your character is currently plummeting to their doom.
*   `IsFlying()` - Returns 1 if flying, otherwise nil.
*   `IsFlyableArea()` - Returns 1 if it is possible to fly here, nil otherwise.
*   `IsIndoors()` - Returns 1 if you are indoors, otherwise nil. Returns nil for indoor areas where you can still mount.
*   `IsMounted()` - Returns 1 if mounted, otherwise nil
*   `IsOutdoors()` - Returns 1 if you are outdoors, otherwise nil. Returns 1 for indoor areas where you can still mount.
*   `IsOutOfBounds()` - Returns 1 if you fell off the map.
*   `IsResting()` - Returns 1 if your character is currently resting.
*   `IsStealthed()` - Returns 1 if stealthed or shadowmeld, otherwise nil
*   `IsSwimming()` - Returns 1 if your character is currently swimming.
*   `IsTitleKnown(index)` - Returns 1 if the title is valid for the player, otherwise 0.
*   `IsXPUserDisabled()` - Returns 1 if the character has disabled experience gain.
*   `NotWhileDeadError()` - Generates an error message saying you cannot do that while dead.
*   `ResurrectHasSickness()` - Appears to be used when accepting a resurrection will give you resurrection sickessness.
*   `ResurrectHasTimer()` - Does the player have to wait before accepting a resurrection
*   `ResurrectGetOfferer()` - Returns the name of the person offering to resurrect you.
*   `RetrieveCorpse()` - Resurrects when near corpse. e.g., The "Accept" button one sees after running back to your body.
*   `SetCurrentTitle(titleId)` - Sets the player's current title id
*   `TargetTotem()` - (added 3.0.8)

### Group

*   `AcceptGroup()`
*   `CanSendSoR()`
*   `CheckSpiritHealerDist()`
*   `ConfirmSummon()`
*   `DeclineGroup()`
*   `GetCorpseMapPosition()`
*   `GetCorpsePosition()`
*   `GetLootMethod()`
*   `GetLootRollTimeLeft()`
*   `GetLootSlotInfo(slot)`
*   `GetLootSlotLink(slot)`
*   `GetLootSourceInfo(slot)`
*   `GetLootSourceLink(slot)`
*   `GetLootThreshold()`
*   `GetMasterLootCandidate(index)`
*   `GetNumLootItems()`
*   `GetNumPartyMembers()`
*   `GetNumRaidMembers()`
*   `GetNumSubgroupMembers()`
*   `GetPartyLeaderIndex()`
*   `GetRaidLeaderIndex()`
*   `GetRaidSubgroup(index)`
*   `GetSubgroupUpdateMember(index)`
*   `GetSummonConfirmAreaName()`
*   `GetSummonConfirmSummoner()`
*   `GetSummonConfirmTimeLeft()`
*   `GiveMasterLoot(index, lootSlot)`
*   `IsEveryoneAssistant()`
*   `IsInGroup()`
*   `IsInRaid()`
*   `IsMasterLooter()`
*   `IsPartyLeader()`
*   `IsRaidLeader()`
*   `IsRollInProgress(rollId)`
*   `LeaveParty()`
*   `PromoteToLeader("unit")`
*   `RollOnLoot(rollId, roll)`
*   `SetLootMethod("lootMethod"[, "masterPlayer" or threshold])`
*   `SetLootThreshold(itemQuality)`
*   `UninviteUnit("name" [, "reason"])`
*   `UnitInParty("unit")`

### Guild

*   `AcceptGuild()`
*   `BuyGuildCharter("guildName")`
*   `CanEditGuildEvent()`
*   `CanEditGuildInfo()`
*   `CanEditMOTD()`
*   `CanEditOfficerNote()`
*   `CanEditPublicNote()`
*   `CanGuildDemote()`
*   `CanGuildInvite()`
*   `CanGuildPromote()`
*   `CanGuildRemove()`
*   `CanViewOfficerNote()`
*   `CloseGuildRegistrar()`
*   `CloseGuildRoster()`
*   `CloseTabardCreation()`
*   `DeclineGuild()`
*   `GetGuildCharterCost()`
*   `GetGuildEventInfo(index)`
*   `GetGuildInfo("unit")`
*   `GetGuildInfoText()`
*   `GetGuildRosterInfo(index)`
*   `GetGuildRosterLastOnline(index)`
*   `GetGuildRosterMOTD()`
*   `GetGuildRosterSelection()`
*   `GetGuildRosterShowOffline()`
*   `GetNumGuildEvents()`
*   `GetNumGuildMembers()`
*   `GetTabardCreationCost()`
*   `GetTabardInfo()`
*   `GuildControlAddRank("name")`
*   `GuildControlDelRank("name")`
*   `GuildControlGetNumRanks()`
*   `GuildControlGetRankFlags()`
*   `GuildControlGetRankName(index)`
*   `GuildControlSaveRank("name")`
*   `GuildControlSetRank(rank)`
*   `GuildControlSetRankFlag(index, enabled)`
*   `GuildDemote("name")`
*   `GuildDisband()`
*   `GuildInfo()`
*   `GuildInvite("name")`
*   `GuildLeave()`
*   `GuildPromote("name")`
*   `GuildRoster()`
*   `GuildRosterSetOfficerNote(index, "note")`
*   `GuildRosterSetPublicNote(index, "note")`
*   `GuildSetMOTD("note")`
*   `GuildSetLeader("name")`
*   `GuildUninvite("name")`
*   `IsGuildLeader("name")`
*   `IsInGuild()`
*   `QueryGuildEventLog()`
*   `SetGuildInfoText()`
*   `SetGuildRosterSelection(index)`
*   `SetGuildRosterShowOffline(enabled)`
*   `SortGuildRoster("sort")`
*   `UnitGetGuildXP("unit")`

### Guild Bank

*   `AutoStoreGuildBankItem(tab, slot)`
*   `BuyGuildBankTab()`
*   `CanGuildBankRepair()`
*   `CanWithdrawGuildBankMoney()`
*   `CloseGuildBankFrame()`
*   `DepositGuildBankMoney(money)`
*   `GetCurrentGuildBankTab()`
*   `GetGuildBankItemInfo(tab, slot)`
*   `GetGuildBankItemLink(tab, slot)`
*   `GetGuildBankMoney()`
*   `GetGuildBankMoneyTransaction(index)`
*   `GetGuildBankTabCost()`
*   `GetGuildBankTabInfo(tab)`
*   `GetGuildBankTabPermissions(tab)`
*   `GetGuildBankText(tab)`
*   `GetGuildBankTransaction(tab, index)`
*   `GetGuildBankWithdrawGoldLimit()`
*   `GetGuildTabardFileNames()`
*   `GetNumGuildBankMoneyTransactions()`
*   `GetNumGuildBankTabs()`
*   `GetNumGuildBankTransactions(tab)`
*   `PickupGuildBankItem(tab, slot)`
*   `PickupGuildBankMoney(money)`
*   `QueryGuildBankLog(tab)`
*   `QueryGuildBankTab(tab)`
*   `SetCurrentGuildBankTab(tab)`
*   `SetGuildBankTabInfo(tab, name, iconIndex)`
*   `SetGuildBankTabPermissions(tab, index, enabled)`
*   `SetGuildBankWithdrawGoldLimit(amount)`
*   `SplitGuildBankItem(tab, slot, amount)`
*   `WithdrawGuildBankMoney(money)`

### Honor

*   `GetHolidayBGHonorCurrencyBonuses()`
*   `GetInspectHonorData()`
*   `GetPVPLifetimeStats()`
*   `GetPVPRankInfo(rank[, unit])`
*   `GetPVPRankProgress()`
*   `GetPVPSessionStats()`
*   `GetPVPYesterdayStats()`
*   `GetRandomBGHonorCurrencyBonuses()`
*   `HasInspectHonorData()`
*   `RequestInspectHonorData()`
*   `UnitPVPName("unit")`
*   `UnitPVPRank("unit")`

### Ignore

*   `AddIgnore("name")`
*   `AddOrDelIgnore("name")`
*   `DelIgnore("name")`
*   `GetIgnoreName(index)`
*   `GetNumIgnores()`
*   `GetSelectedIgnore()`
*   `SetSelectedIgnore(index)`

### Inspection

*   `CanInspect("unit"[, showError])`
*   `CheckInteractDistance("unit", interaction)`
*   `ClearInspectPlayer()`
*   `GetInspectArenaTeamData(index)`
*   `HasInspectHonorData()`
*   `RequestInspectHonorData()`
*   `GetInspectHonorData()`
*   `NotifyInspect("unit")`
*   `InspectUnit("unit")`

### Instance

*   `CanShowResetInstances()`
*   `GetBattlefieldInstanceExpiration()`
*   `GetBattlefieldInstanceInfo(index)`
*   `GetBattlefieldInstanceRunTime()`
*   `GetInstanceBootTimeRemaining()`
*   `GetInstanceDifficulty()`
*   `GetInstanceInfo()`
*   `GetInstanceLockTimeRemaining(raidIndex)`
*   `GetNumSavedInstances()`
*   `GetSavedInstanceInfo(raidIndex)`
*   `GetSavedInstanceEncounterInfo(instanceIndex, encounterIndex)`
*   `IsInInstance()`
*   `ResetInstances()`
*   `SetDungeonDifficulty(difficulty)`
*   `SetInstanceLeader("unit")`
*   `SetRaidDifficulty(difficulty)`

### Item

*   `ContainerIDToInventoryID(bagID)`
*   `GetContainerItemCooldown(bagid, slot)`
*   `GetContainerItemDurability(bagid, slot)`
*   `GetContainerItemInfo(bagid, slot)`
*   `GetContainerItemLink(bagid, slot)`
*   `GetContainerNumSlots(bagid)`
*   `GetContainerNumFreeSlots(bagid)`
*   `GetInventoryItemBroken(unit, slot)`
*   `GetInventoryItemCooldown(unit, slot)`
*   `GetInventoryItemCount(unit, slot)`
*   `GetInventoryItemDurability(unit, slot)`
*   `GetInventoryItemLink(unit, slot)`
*   `GetInventoryItemQuality(unit, slot)`
*   `GetInventoryItemTexture(unit, slot)`
*   `GetInventorySlotInfo(slot)`
*   `GetItemCooldown(itemId)`
*   `GetItemCount(itemId[, includeBank][, includeCharges])`
*   `GetItemFamily(itemId)`
*   `GetItemGem(itemLink, [gemIndex])`
*   `GetItemInfo(itemId)`
*   `GetItemQualityColor(quality)`
*   `GetItemStats(itemLink)`
*   `GetMerchantItemCostInfo(index)`
*   `GetMerchantItemCostItem(index, item)`
*   `GetMerchantItemInfo(index)`
*   `GetMerchantItemLink(index)`
*   `GetMerchantNumItems()`
*   `GetNewItemAlert()`
*   `GetTradeSkillIcon(tradeSkillID)`
*   `GetTradeSkillInfo(tradeSkillID, [filter])`
*   `GetTradeSkillItemLink(tradeSkillID)`
*   `GetTradeSkillLine()`
*   `GetTradeSkillNumMade(tradeSkillID)`
*   `GetTradeSkillReagentInfo(tradeSkillID, reagentID)`
*   `GetTradeSkillReagentItemLink(tradeSkillID, reagentID)`
*   `GetTradeSkillSelectionIndex()`
*   `GetTradeSkillSubClasses(index)`
*   `GetTradeSkillTools(tradeSkillID)`
*   `HasFullBag()`
*   `IsConsumableItem(itemId)`
*   `IsCurrentItem(itemId)`
*   `IsEquippableItem(itemId)`
*   `IsEquippedItem(itemId)`
*   `IsEquippedItemType(itemType)`
*   `IsUsableItem(itemId)`
*   `PickupContainerItem(bagid, slot)`
*   `PickupInventoryItem(slot)`
*   `PickupItem(itemId)`
*   `PickupMerchantItem(index)`
*   `PickupTradePlayerItem(id)`
*   `PickupTradeTargetItem(id)`
*   `PlaceAuctionBid(type, index, bid)`
*   `PutItemInBag(bagid)`
*   `PutItemInSlot(slot)`
*   `ReplaceEnchant()`
*   `ReplaceTradeEnchant()`
*   `SocketInventoryItem(slot)`
*   `SocketContainerItem(bagid, slot)`
*   `SplitContainerItem(bagid, slot, amount)`
*   `UseContainerItem(bagid, slot)`
*   `UseInventoryItem(slot)`
*   `UseItemByName(itemName)`

### Loot

*   `ConfirmLootRoll(rollId, roll)`
*   `ConfirmLootSlot(slot)`
*   `GetLootInfo()`
*   `GetLootMethod()`
*   `GetLootRollTimeLeft()`
*   `GetLootSlotInfo(slot)`
*   `GetLootSlotLink(slot)`
*   `GetLootSourceInfo(slot)`
*   `GetLootSourceLink(slot)`
*   `GetLootThreshold()`
*   `GetMasterLootCandidate(index)`
*   `GetNumLootItems()`
*   `GiveMasterLoot(index, lootSlot)`
*   `IsRollInProgress(rollId)`
*   `LootSlot(slot)`
*   `RollOnLoot(rollId, roll)`
*   `SetLootMethod("lootMethod"[, "masterPlayer" or threshold])`
*   `SetLootThreshold(itemQuality)`

### Macro

*   `CreateMacro(name, icon, body[, perCharacter])`
*   `DeleteMacro(id)`
*   `EditMacro(id, name, icon, body[, perCharacter])`
*   `GetMacroBody(id)`
*   `GetMacroInfo(id)`
*   `GetNumMacros()`
*   `PickupMacro(id)`
*   `RunMacro(id)`
*   `RunMacroText(macroText)`
*   `StopMacro()`

### Mail

*   `CheckInbox()`
*   `ClearSendMail()`
*   `ClickSendMailItemButton(index)`
*   `CloseMail()`
*   `DeleteInboxItem(index)`
*   `GetInboxHeaderInfo(index)`
*   `GetInboxItem(index)`
*   `GetInboxItemLink(index)`
*   `GetInboxNumItems()`
*   `GetInboxText(index)`
*   `GetMoneyMailMoney(index)`
*   `GetSendMailCOD()`
*   `GetSendMailItem(index)`
*   `GetSendMailItemLink(index)`
*   `GetSendMailMoney()`
*   `GetSendMailPrice()`
*   `HasNewMail()`
*   `InboxItemCanDelete(index)`
*   `ReturnInboxItem(index)`
*   `SendMail(recipient, subject, body)`
*   `SetSendMailCOD(cod)`
*   `SetSendMailMoney(money)`
*   `TakeInboxItem(index)`
*   `TakeInboxMoney(index)`

### Map

*   `GetMapContinents()`
*   `GetMapDebugObjectInfo(index)`
*   `GetMapInfo()`
*   `GetMapLandmarks()`
*   `GetMapOverlayInfo(overlay)`
*   `GetMapZones(continent)`
*   `GetPlayerMapPosition(unit)`
*   `GetWorldMapArrowPosition()`
*   `SetMapByID(mapID)`
*   `SetMapToCurrentZone()`
*   `SetMapZoom(continent[, zone])`
*   `WorldMapFrame_LoadContinents()`
*   `WorldMapFrame_LoadZones(continent)`
*   `WorldMapPing(map)`

### Merchant

*   `BuyMerchantItem(index, quantity)`
*   `CloseMerchant()`
*   `GetBuybackItemInfo(index)`
*   `GetBuybackItemLink(index)`
*   `GetMerchantItemCostInfo(index)`
*   `GetMerchantItemCostItem(index, item)`
*   `GetMerchantItemInfo(index)`
*   `GetMerchantItemLink(index)`
*   `GetMerchantNumItems()`
*   `ShowMerchantSellCursor(value)`

### Pet

*   `AbandonPet()`
*   `BuyStableSlot()`
*   `GetPetActionCooldown(index)`
*   `GetPetActionInfo(index)`
*   `GetPetActionSlotUsable(index)`
*   `GetPetActionsUsable()`
*   `GetPetExperience()`
*   `GetPetFoodTypes()`
*   `GetPetHappiness()`
*   `GetPetIcon()`
*   `GetPetLoyalty()`
*   `GetPetTimeRemaining()`
*   `GetStablePetInfo(index)`
*   `GetNumStablePets()`
*   `HasPetSpells()`
*   `HasPetUI()`
*   `IsPetActive()`
*   `IsPetAttackActive()`
*   `PetAbandon()`
*   `PetAggressiveMode()`
*   `PetAttack()`
*   `PetCanBeAbandoned()`
*   `PetCanBeDismissed()`
*   `PetCanBeRenamed()`
*   `PetDefensiveMode()`
*   `PetDismiss()`
*   `PetFollow()`
*   `PetHasActionBar()`
*   `PetPassiveMode()`
*   `PetRename("name")`
*   `PetWait()`
*   `PickupPetAction(slot)`
*   `PickupStablePet(index)`
*   `SetPetStablePaperdoll(modelObject)`
*   `TogglePetAutocast(index)`
*   `ToggleSpellAutocast("spellName" | spellId, bookType)`
*   `GetSpellAutocast("spellName" | spellId, bookType)`

### Petition

*   `CanSignPetition()`
*   `ClosePetition()`
*   `GetNumPetitionNames()`
*   `GetPetitionInfo()`
*   `GetPetitionNameInfo(index)`
*   `OfferPetition()`
*   `RenamePetition("name")`
*   `SignPetition()`
*   `TurnInGuildCharter()`

### Quest

*   `AbandonQuest()`
*   `AcceptQuest()`
*   `AddQuestWatch(questIndex[, watchTime])`
*   `AddWorldQuestWatch(questId)`
*   `CloseQuest()`
*   `CollapseQuestHeader()`
*   `CompleteQuest()`
*   `ConfirmAcceptQuest()`
*   `DeclineQuest()`
*   `ExpandQuestHeader()`
*   `GetAbandonQuestName()`
*   `GetActiveLevel(index)`
*   `GetActiveTitle(index)`
*   `GetAvailableLevel(index)`
*   `GetAvailableTitle(index)`
*   `GetAvailableQuestInfo(index)`
*   `GetGreetingText()`
*   `GetNumActiveQuests()`
*   `GetNumAvailableQuests()`
*   `GetNumQuestChoices()`
*   `GetNumQuestItems()`
*   `GetNumQuestLeaderBoards([questIndex])`
*   `GetNumQuestLogChoices()`
*   `GetNumQuestLogEntries()`
*   `GetNumQuestLogRewards()`
*   `GetNumQuestRewards()`
*   `GetNumQuestWatches()`
*   `GetObjectiveText()`
*   `GetProgressText()`
*   `GetQuestBackgroundMaterial()`
*   `GetQuestGreenRange()`
*   `GetQuestIndexForTimer()`
*   `GetQuestIndexForWatch(watchIndx)`
*   `GetQuestItemInfo()`
*   `GetQuestItemLink()`
*   `GetQuestLink(index)`
*   `GetQuestLogChoiceInfo()`
*   `GetQuestLogGroupNum()`
*   `GetQuestLogItemLink()`
*   `GetQuestLogLeaderBoard(ldrIndex[, questIndex])`
*   `GetQuestLogPushable()`
*   `GetQuestLogQuestText()`
*   `GetQuestLogRequiredMoney()`
*   `GetQuestLogRewardInfo()`
*   `GetQuestLogRewardMoney()`
*   `GetQuestLogRewardSpell()`
*   `GetQuestLogRewardTalents()`
*   `GetQuestLogRewardXP()`
*   `GetQuestLogSelection()`
*   `GetQuestLogTimeLeft()`
*   `GetQuestLogTitle(index)`
*   `GetQuestMoneyToGet()`
*   `GetQuestReward(rewardIndex)`
*   `GetQuestText()`
*   `GetQuestTimers()`
*   `GetRewardArenaPoints()`
*   `GetRewardHonor()`
*   `GetRewardMoney()`
*   `GetRewardSpell()`
*   `GetRewardTalents()`
*   `GetRewardText()`
*   `GetRewardTitle()`
*   `GetRewardXP()`
*   `GetTitleText()`
*   `IsCurrentQuestFailed()`
*   `IsQuestCompletable()`
*   `IsQuestWatched(questIndex)`
*   `IsWorldQuestWatched(questId)`
*   `IsUnitOnQuest(questIndex, "unit")`
*   `QuestChooseRewardError()`
*   `QuestFlagsPVP()`
*   `QuestGetAutoAccept()`
*   `QuestLogPushQuest()`
*   `RemoveQuestWatch(questIndex)`
*   `RemoveWorldQuestWatch(questId)`
*   `SelectActiveQuest()`
*   `SelectAvailableQuest()`
*   `SelectQuestLogEntry()`
*   `SetAbandonQuest()`
*   `ShiftQuestWatches(id1, id2)`
*   `SortQuestWatches()`
*   `GetDailyQuestsCompleted()`
*   `GetMaxDailyQuests()`
*   `GetQuestResetTime()`
*   `WatchFrame_Update()`
*   `QueryQuestsCompleted()`
*   `GetQuestsCompleted([table])`
*   `QuestIsDaily()`
*   `QuestIsWeekly()`

### Raid

*   `ClearRaidMarker()`
*   `ConvertToRaid()`
*   `ConvertToParty()`
*   `DemoteAssistant("unit")`
*   `GetAllowLowLevelRaid()`
*   `GetPartyAssignment("assignment")`
*   `GetRaidRosterInfo(raidIndex)`
*   `GetRaidRosterSelection()`
*   `GetRaidTargetIndex("unit")`
*   `GetReadyCheckStatus("unit")`
*   `InitiateRolePoll()`
*   `PlaceRaidMarker(index)`
*   `PromoteToAssistant("unit")`
*   `RequestRaidInfo()`
*   `SetPartyAssignment("assignment", player)`
*   `SetAllowLowLevelRaid(allowed)`
*   `SetRaidRosterSelection(index)`
*   `SetRaidSubgroup(index, subgroup)`
*   `SwapRaidSubgroup(index1, index2)`
*   `SetRaidTarget("unit", index)`

### Spell

*   `CastShapeshiftForm(index)`
*   `CastSpell(index, bookType)`
*   `CastSpellByName(name[, onSelf])`
*   `GetSpellBookItemInfo(index, bookType)`
*   `GetSpellBookItemName(index, bookType)`
*   `GetSpellBookItemTexture(index, bookType)`
*   `GetSpellBonusDamage(school)`
*   `GetSpellBonusHealing()`
*   `GetSpellCooldown(spellId)`
*   `GetSpellCritChance(school)`
*   `GetSpellCritChanceFromRanged()`
*   `GetSpellHitModifier()`
*   `GetSpellInfo(spellId)`
*   `GetSpellLink(spellId)`
*   `GetSpellName(spellId)`
*   `GetSpellPenetration()`
*   `GetSpellRank(spellId)`
*   `GetSpellTexture(spellId)`
*   `IsSpellInRange(spellId)`
*   `IsUsableSpell(spellId)`
*   `PickupSpell(spellId)`
*   `SpellCanTargetUnit("unit")`
*   `SpellHasRange(spellId)`
*   `SpellIsTargeting()`
*   `SpellStopCasting()`
*   `SpellStopTargeting()`
*   `SpellTargetUnit("unit")`

### System

*   `CanExitVehicle()`
*   `CanJoinBattlefieldAsGroup()`
*   `C_Timer.After(seconds, callback)`
*   `C_Timer.NewTicker(seconds, callback)`
*   `C_Timer.NewTimer(seconds, callback)`
*   `debugprofilestart()`
*   `debugprofilestop()`
*   `ForceQuit()`
*   `GetAddOnCPUUsage()`
*   `GetAddOnEnableState(character, addon)`
*   `GetAddOnInfo(addon)`
*   `GetAddOnMemoryUsage()`
*   `GetCVar(cvar)`
*   `GetCVarBool(cvar)`
*   `GetCVarDefault(cvar)`
*   `GetCVarInfo(cvar)`
*   `GetFramerate()`
*   `GetGameTime()`
*   `GetLocale()`
*   `GetNetStats()`
*   `GetNumAddOns()`
*   `GetRealmName()`
*   `GetScreenHeight()`
*   `GetScreenWidth()`
*   `GetTime()`
*   `GetTimeToWellRested()`
*   `IsAddOnLoaded(addon)`
*   `IsControlKeyDown()`
*   `IsLeftAltKeyDown()`
*   `IsLeftControlKeyDown()`
*   `IsLeftShiftKeyDown()`
*   `IsRightAltKeyDown()`
*   `IsRightControlKeyDown()`
*   `IsRightShiftKeyDown()`
*   `IsShiftKeyDown()`
*   `IsStereoVideoAvailable()`
*   `LoadAddOn(addon)`
*   `Logout()`
*   `PlaySound(sound)`
*   `PlaySoundFile(filePath)`
*   `Quit()`
*   `Random()`
*   `RegisterCVar(cvar, value)`
*   `ReloadUI()`
*   `RestartGx()`
*   `RunScript(script)`
*   `SaveChanges()`
*   `Screenshot()`
*   `ScriptErrors()`
*   `SetCVar(cvar, value)`
*   `SetGamma(gamma)`
*   `SetLayoutMode()`
*   `SetSavedInstanceExtent(extent)`
*   `StopMusic()`
*   `TakeScreenshot()`
*   `UpdateAddOnCPUUsage()`
*   `UpdateAddOnMemoryUsage()`

### Talent

*   `AddPreviewTalentPoints(points)`
*   `ApplyTalent()`
*   `GetActiveTalentGroup()`
*   `GetNumTalentGroups()`
*   `GetNumTalents(tab)`
*   `GetTalentInfo(tab, index)`
*   `GetTalentLink(tab, index)`
*   `GetTalentPrereqs(tab, index)`
*   `GetTalentRank(tab, index)`
*   `LearnTalent(tab, index)`
*   `RemovePreviewTalentPoints(points)`
*   `SetActiveTalentGroup(group)`

### Targeting

*   `TargetLastEnemy()`
*   `TargetLastTarget()`
*   `TargetNearestEnemy()`
*   `TargetNearestFriend()`
*   `TargetNearestPartyMember()`
*   `TargetNearestRaidMember()`
*   `TargetOfTarget()`
*   `TargetTotem()`
*   `TargetUnit("unit")`

### Taxi

*   `GetNumRoutes()`
*   `GetTaxiBenchmarkMode()`
*   `GetTaxiMapID()`
*   `GetTaxiRouteInfo(route)`
*   `IsTaxiBenchmarkMode()`
*   `NumTaxiNodes()`
*   `SetTaxiBenchmarkMode(mode)`
*   `TakeTaxiNode(node)`
*   `TaxiGetSrcLocation()`
*   `TaxiGetDestLocation()`
*   `TaxiGetDestSize()`
*   `TaxiGetSrcSize()`
*   `TaxiIsDirectPath()`
*   `TaxiNodeCost(node)`
*   `TaxiNodeName(node)`
*   `TaxiNodePosition(node)`
*   `TaxiNodeGetType(node)`
*   `TaxiRequestEarlyLanding()`

### Trade Skill

*   `CloseTradeSkill()`
*   `CollapseTradeSkillSubClass(subClass)`
*   `DoTradeSkill(index, num)`
*   `ExpandTradeSkillSubClass(subClass)`
*   `GetTradeSkillCooldown(index)`
*   `GetTradeSkillDescription(index)`
*   `GetTradeSkillIcon(index)`
*   `GetTradeSkillInfo(index)`
*   `GetTradeSkillItemLink(index)`
*   `GetTradeSkillLine()`
*   `GetTradeSkillNumMade(index)`
*   `GetTradeSkillReagentInfo(index, reagent)`
*   `GetTradeSkillReagentItemLink(index, reagent)`
*   `GetTradeSkillSelectionIndex()`
*   `GetTradeSkillSubClasses(index)`
*   `GetTradeSkillTools(index)`
*   `IsTradeSkillLinked()`
*   `SelectTradeSkill(index)`
*   `SetTradeSkillInvSlotFilter(slotIndex, onOff[, exclusive])`
*   `SetTradeSkillSubClassFilter(slotIndex, onOff[, exclusive])`
*   `StopTradeSkillRepeat()`
*   `TradeSkillOnlyShowMakeable(onlyMakable)`

### Tracking

*   `GetNumTrackingTypes()`
*   `GetTrackingInfo(id)`
*   `SetTracking(id)`

### Trading

*   `AcceptTrade()`
*   `AddTradeMoney()`
*   `CancelTrade()`
*   `CancelTradeAccept()`
*   `ClickTargetTradeButton(index)`
*   `ClickTradeButton(index)`
*   `CloseTrade()`
*   `GetPlayerTradeMoney()`
*   `GetTargetTradeMoney()`
*   `GetTradePlayerItemInfo(id)`
*   `GetTradePlayerItemLink(id)`
*   `GetTradeTargetItemInfo(id)`
*   `GetTradeTargetItemLink(id)`
*   `InitiateTrade(UnitId)`
*   `PickupPlayerMoney(copper)`
*   `PickupTradeMoney(copper)`
*   `SetTradeMoney(copper)`
*   `ReplaceTradeEnchant()`

### Training

*   `BuyTrainerService(index)`
*   `CloseTrainer()`
*   `GetNumTrainerServices()`
*   `GetTrainerGreetingText()`
*   `GetTrainerSelectionIndex()`
*   `GetTrainerServiceAbilityReq(trainerIndex,reqIndex)`
*   `GetTrainerServiceCost(index)`
*   `GetTrainerServiceDescription(index)`
*   `GetTrainerServiceIcon(index)`
*   `GetTrainerServiceInfo(index)`
*   `GetTrainerServiceItemLink(index)`
*   `GetTrainerServiceLevelReq(index)`
*   `GetTrainerServiceNumAbilityReq()`
*   `GetTrainerServiceSkillLine(index)`
*   `GetTrainerServiceSkillReq(index)`
*   `GetTrainerServiceTypeFilter("filter")`
*   `IsTradeskillTrainer()`
*   `OpenTrainer()`
*   `SelectTrainerService()`
*   `SetTrainerServiceTypeFilter("filter",state)`

### Unit

*   `AssistUnit("unit")`
*   `CheckInteractDistance("unit",distIndex)`
*   `DropItemOnUnit("unit")`
*   `FollowUnit("unit")`
*   `FocusUnit("unit")`
*   `ClearFocus()`
*   `GetUnitName("unit", showServerName)`
*   `GetUnitPitch("unit")`
*   `GetUnitSpeed("unit")`
*   `InviteUnit("name" or "unit")`
*   `IsUnitOnQuest(questIndex, "unit")`
*   `SpellCanTargetUnit("unit")`
*   `SpellTargetUnit("unit")`
*   `TargetUnit("unit")`
*   `UnitAffectingCombat("unit")`
*   `UnitArmor("unit")`
*   `UnitAttackBothHands("unit")`
*   `UnitAttackPower("unit")`
*   `UnitAttackSpeed("unit")`
*   `UnitAura("unit", index [, filter])`
*   `UnitBuff("unit", index [,raidFilter])`
*   `UnitCanAssist("unit", "otherUnit")`
*   `UnitCanAttack("unit", "otherUnit")`
*   `UnitCanCooperate("unit", "otherUnit")`
*   `UnitClass("unit")`
*   `UnitClassification("unit")`
*   `UnitCreatureFamily("unit")`
*   `UnitCreatureType("unit")`
*   `UnitDamage("unit")`
*   `UnitDebuff("unit", index [,raidFilter])`
*   `UnitDefense("unit")`
*   `UnitDetailedThreatSituation("unit", "mob")`
*   `UnitExists("unit")`
*   `UnitFactionGroup("unit")`
*   `UnitGroupRolesAssigned("unit")`
*   `UnitGUID("unit")`
*   `GetPlayerInfoByGUID("guid")`
*   `UnitHasLFGDeserter("unit")`
*   `UnitHasLFGRandomCooldown("unit")`
*   `UnitHasRelicSlot("unit")`
*   `UnitHealth("unit")`
*   `UnitHealthMax("unit")`
*   `UnitInParty("unit")`
*   `UnitInRaid("unit")`
*   `UnitInBattleground("unit")`
*   `UnitIsInMyGuild("unit")`
*   `UnitInRange("unit")`
*   `UnitIsAFK("unit")`
*   `UnitIsCharmed("unit")`
*   `UnitIsConnected("unit")`
*   `UnitIsCorpse("unit")`
*   `UnitIsDead("unit")`
*   `UnitIsDeadOrGhost("unit")`
*   `UnitIsDND("unit")`
*   `UnitIsEnemy("unit", "otherUnit")`
*   `UnitIsFeignDeath("unit")`
*   `UnitIsFriend("unit", "otherUnit")`
*   `UnitIsGhost("unit")`
*   `UnitIsPVP("unit")`
*   `UnitIsPVPFreeForAll("unit")`
*   `UnitIsPVPSanctuary("unit")`
*   `UnitIsPlayer("unit")`
*   `UnitIsPossessed("unit")`
*   `UnitIsSameServer("unit", "otherUnit")`
*   `UnitIsTapped("unit")`
*   `UnitIsTappedByPlayer("unit")`
*   `UnitIsTappedByAllThreatList("unit")`
*   `UnitIsTrivial("unit")`
*   `UnitIsUnit("unit", "otherUnit")`
*   `UnitIsVisible("unit")`
*   `UnitLevel("unit")`
*   `UnitMana("unit")`
*   `UnitManaMax("unit")`
*   `UnitName("unit")`
*   `UnitOnTaxi("unit")`
*   `UnitPlayerControlled("unit")`
*   `UnitPlayerOrPetInParty("unit")`
*   `UnitPlayerOrPetInRaid("unit")`
*   `UnitPVPName("unit")`
*   `UnitPVPRank("unit")`
*   `UnitPower("unit"[,type])`
*   `UnitPowerMax("unit"[,type])`
*   `UnitPowerType("unit")`
*   `UnitRace("unit")`
*   `UnitRangedAttack("unit")`
*   `UnitRangedAttackPower("unit")`
*   `UnitRangedDamage("unit")`
*   `UnitReaction("unit", "otherUnit")`
*   `UnitResistance("unit", "resistanceIndex")`
*   `UnitSex("unit")`
*   `UnitStat("unit", stat)`
*   `UnitThreatSituation("unit"[, mob])`
*   `UnitUsingVehicle("unit")`
*   `VehicleExit()`

### Voice Chat

*   `GetChannelDisplayInfo(channel)`
*   `GetNumVoiceSessions()`
*   `GetTalkerInfo(channel, index)`
*   `GetVoiceSessionInfo(index)`
*   `IsSilenced(channel)`
*   `IsSpeakerEnabled(channel)`
*   `IsVoiceChatAllowed()`
*   `IsVoiceChatAllowedByServer()`
*   `IsVoiceChatEnabled()`
*   `SetChannelVolume(channel, volume)`
*   `SetMasterVolume(volume)`
*   `SetMicVolume(volume)`
*   `SetSilenced(channel, silenced)`
*   `SetSpeakerEnabled(channel, enabled)`
*   `VoiceChat_GetMute(channel, index)`
*   `VoiceChat_GetVolume(channel, index)`
*   `VoiceChat_IsPlaying(channel, index)`
*   `VoiceChat_IsSelfMuted()`
*   `VoiceChat_IsSpeaking(channel, index)`
*   `VoiceChat_SetMute(channel, index, mute)`
*   `VoiceChat_SetVolume(channel, index, volume)`

### Who

*   `GetWhoInfo(index)`
*   `SendWho(query)`
*   `SetWhoToUI(show)`

### World

*   `GetMinimapZoneText()`
*   `GetRealZoneText()`
*   `GetSubZoneText()`
*   `GetZonePVPInfo()`
*   `GetZoneText()`
*   `SetCurrentMapDungeonLevel(level)`
*   `SetDungeonMapLevel(level)`

## Events

This is a list of events available in the WoW 3.3.5a API, compiled from the WoWWiki archive.

*   `ACHIEVEMENT_EARNED`
*   `ACTIONBAR_HIDEGRID`
*   `ACTIVE_TALENT_GROUP_CHANGED`
*   `ADDON_LOADED`
*   `AREA_SPIRIT_HEALER_IN_RANGE`
*   `AREA_SPIRIT_HEALER_OUT_OF_RANGE`
*   `AUCTION_HOUSE_SHOW`
*   `CHAT_MSG_ACHIEVEMENT`
*   `CHAT_MSG_CHANNEL`
*   `CHAT_MSG_GUILD`
*   `CHAT_MSG_LOOT`
*   `CHAT_MSG_SAY`
*   `CHAT_MSG_YELL`
*   `COMBAT_LOG_EVENT`
*   `COMBAT_LOG_EVENT_UNFILTERED`
*   `CURRENT_SPELL_CAST_CHANGED`
*   `CRITERIA_UPDATE`
*   `ZONE_CHANGED`
*   `ZONE_CHANGED_INDOORS`

## Widget API

This is a list of widgets and their methods available in the WoW 3.3.5a API, compiled from the WoWWiki archive.

*   Source: https://wowwiki-archive.fandom.com/wiki/Widget_API

### Root Widgets

*   **Object**: Abstract base type for all UI objects.
    *   `GetParent()`
*   **UIObject**: Abstract base type for all widget types.
    *   `GetAlpha()`
    *   `GetName()`
    *   `GetObjectType()`
    *   `IsForbidden()`
    *   `IsObjectType("type")`
    *   `SetAlpha(alpha)`

### UIObject Derivatives

*   **AnimationGroup**: Manages playback, order, and looping of its child Animations.
    *   `Play()`
    *   `Pause()`
    *   `Stop()`
    *   `Finish()`
    *   `GetProgress()`
    *   `IsDone()`
    *   `IsPlaying()`
    *   `IsPaused()`
    *   `GetDuration()`
    *   `SetLooping(loopType)`
    *   `GetLooping()`
    *   `GetLoopState()`
    *   `CreateAnimation("animationType", ["name"[, "inheritsFrom"]])`
    *   `HasScript("handler")`
    *   `GetScript("handler")`
    *   `SetScript("handler", function)`
*   **Animation**: Base animation type for animations in an AnimationGroup.
    *   `Play()`
    *   `Pause()`
    *   `Stop()`
    *   `IsDone()`
    *   `IsPlaying()`
    *   `IsPaused()`
    *   `IsStopped()`
    *   `IsDelaying()`
    *   `GetElapsed()`
    *   `SetStartDelay(delaySec)`
    *   `GetStartDelay()`
    *   `SetEndDelay(delaySec)`
    *   `GetEndDelay()`
    *   `SetDuration(durationSec)`
    *   `GetDuration()`
    *   `GetProgress()`
    *   `GetSmoothProgress()`
    *   `GetProgressWithDelay()`
    *   `SetMaxFramerate(framerate)`
    *   `GetMaxFramerate()`
    *   `SetOrder(order)`
    *   `GetOrder()`
    *   `SetSmoothing(smoothType)`
    *   `GetSmoothing()`
    *   `SetParent(animGroup or "animGroupName")`
    *   `GetRegionParent()`
*   **FontInstance**: Abstract object type that provides font related methods.
    *   `GetFont()`
    *   `GetFontObject()`
    *   `GetJustifyH()`
    *   `GetJustifyV()`
    *   `GetShadowColor()`
    *   `GetShadowOffset()`
    *   `GetSpacing()`
    *   `GetTextColor()`
    *   `SetFont("path", height[, "flags"])`
    *   `SetFontObject(fontObject)`
    *   `SetJustifyH("justifyH")`
    *   `SetJustifyV("justifyV")`
    *   `SetShadowColor(r, g, b[, a])`
    *   `SetShadowOffset(x, y)`
    *   `SetSpacing(spacing)`
    *   `SetTextColor(r, g, b[, a])`
*   **Region**: Abstract object type which cannot actually be created. Defines a potentially visible area.
    *   `ClearAllPoints()`
    *   `CreateAnimationGroup(["name"[, "inheritsFrom"]])`
    *   `GetAnimationGroups()`
    *   `GetBottom()`
    *   `GetCenter()`
    *   `GetHeight()`
    *   `GetLeft()`
    *   `GetNumPoints()`
    *   `GetPoint(pointNum)`
    *   `GetRect()`
    *   `GetRight()`
    *   `GetSize()`
    *   `GetTop()`
    *   `GetWidth()`
    *   `Hide()`
    *   `IsDragging()`
    *   `IsProtected()`
    *   `IsShown()`
    *   `IsVisible()`
    *   `SetAllPoints(frame or "frameName")`
    *   `SetHeight(height)`
    *   `SetParent(parent or "parentName")`
    *   `SetPoint("point", "relativeFrame" or relativeObject, "relativePoint"[, xOfs, yOfs])`
    *   `SetSize(width, height)`
    *   `SetWidth(width)`
    *   `Show()`
    *   `StopAnimating()`

### Frame Derivatives

*   **Button**: A clickable region.
    *   `Click("button")`
    *   `Disable()`
    *   `Enable()`
    *   `GetButtonState()`
    *   `GetDisabledFontObject()`
    *   `GetDisabledTextColor()`
    *   `GetDisabledTexture()`
    *   `GetFontString()`
    *   `GetHighlightFontObject()`
    *   `GetHighlightTextColor()`
    *   `GetHighlightTexture()`
    *   `GetNormalFontObject()`
    *   `GetNormalTexture()`
    *   `GetPushedTextOffset()`
    *   `GetPushedTexture()`
    *   `GetText()`
    *   `GetTextHeight()`
    *   `GetTextWidth()`
    *   `IsEnabled()`
    *   `LockHighlight()`
    *   `RegisterForClicks("button1"[, "button2", ...])`
    *   `RegisterForDrag("button1"[, "button2", ...])`
    *   `SetButtonState("state"[, locked])`
    *   `SetDisabledFontObject(fontObject)`
    *   `SetDisabledTextColor(r, g, b[, a])`
    *   `SetDisabledTexture(texture)`
    *   `SetFontString(fontString)`
    *   `SetHighlightFontObject(fontObject)`
    *   `SetHighlightTextColor(r, g, b[, a])`
    *   `SetHighlightTexture(texture)`
    *   `SetNormalFontObject(fontObject)`
    *   `SetNormalTexture(texture)`
    *   `SetPushedTextOffset(x, y)`
    *   `SetPushedTexture(texture)`
    *   `SetText(text)`
    *   `UnlockHighlight()`
*   **CheckButton**: A button that can be toggled on or off.
    *   `GetChecked()`
    *   `GetCheckedTexture()`
    *   `GetDisabledCheckedTexture()`
    *   `SetChecked([state])`
    *   `SetCheckedTexture(texture)`
    *   `SetDisabledCheckedTexture(texture)`
*   **Cooldown**: A frame that displays a cooldown spiral.
    *   `GetCooldown()`
    *   `GetCooldownDuration()`
    *   `SetCooldown(start, duration)`
*   **ColorSelect**: A frame that allows the user to select a color.
    *   `GetColorAlpha()`
    *   `GetColorRGB()`
    *   `GetColorWheels()`
    *   `SetColorAlpha(alpha)`
    *   `SetColorRGB(r, g, b)`
    *   `SetColorWheelTexture(texture)`
*   **EditBox**: A box that allows the user to enter text.
    *   `AddHistoryLine(text)`
    *   `ClearFocus()`
    *   `GetBlinkSpeed()`
    *   `GetCursorPosition()`
    *   `GetHistoryLines()`
    *   `GetHyperlinksEnabled()`
    *   `GetMaxBytes()`
    *   `GetMaxLetters()`
    *   `GetNumLetters()`
    *   `GetNumber()`
    *   `GetText()`
    *   `GetTextInsets()`
    *   `HasFocus()`
    *   `HighlightText([start, end])`
    *   `Insert(text)`
    *   `IsAutoFocus()`
    *   `IsMultiLine()`
    *   `IsNumeric()`
    *   `IsPassword()`
    *   `SetAutoFocus(isAutoFocus)`
    *   `SetBlinkSpeed(speed)`
    *   `SetCursorPosition(position)`
    *   `SetFocus()`
    *   `SetFont("path", height[, "flags"])`
    *   `SetFontObject(fontObject)`
    *   `SetHyperlinksEnabled(enabled)`
    *   `SetMaxBytes(maxBytes)`
    *   `SetMaxLetters(maxLetters)`
    *   `SetMultiLine(isMultiLine)`
    *   `SetNumber(number)`
    *   `SetNumeric(isNumeric)`
    *   `SetPassword(isPassword)`
    *   `SetText(text)`
    *   `SetTextInsets(left, right, top, bottom)`
*   **GameTooltip**: A tooltip that displays information about game objects.
    *   `AddLine(text[, r, g, b])`
    *   `AddDoubleLine(leftText, rightText[, r1, g1, b1[, r2, g2, b2]])`
    *   `AppendText(text)`
    *   `ClearLines()`
    *   `FadeOut()`
    *   `NumLines()`
    *   `SetAuctionCompareItem(type, index)`
    *   `SetAuctionItem(type, index)`
    *   `SetBagItem(bag, slot)`
    *   `SetBuybackItem(index)`
    *   `SetCurrencyToken(tokenId)`
    *   `SetFrameStack(showhidden)`
    *   `SetGlyph(id)`
    *   `SetGuildBankItem(tab, id)`
    *   `SetHyperlink("itemString" or "itemLink")`
    *   `SetHyperlinkCompareItem("itemLink", index)`
    *   `SetInboxItem(index)`
    *   `SetInventoryItem(unit, slot[, nameOnly])`
    *   `SetLootItem(slot)`
    *   `SetLootRollItem(id)`
    *   `SetMerchantCompareItem("slot"[, offset])`
    *   `SetMerchantItem(index)`
    *   `SetMinimumWidth(width)`
    *   `SetOwner(owner, "anchor"[, +x, +y])`
    *   `SetPadding(padding)`
    *   `SetPetAction(slot)`
    *   `SetQuestItem(type, index)`
    *   `SetQuestLogItem(type, index)`
    *   `SetQuestLogRewardSpell()`
    *   `SetQuestRewardSpell()`
    *   `SetSendMailItem(index)`
    *   `SetShapeshift(slot)`
    *   `SetSpell(spellId, bookType)`
    *   `SetTalent(tabIndex, talentIndex)`
    *   `SetText("text"[, red, green, blue[, alpha[, textWrap]]])`
    *   `SetTracking(id)`
    *   `SetTradePlayerItem(id)`
    *   `SetTradeSkillItem(index)`
    *   `SetTradeTargetItem(id)`
    *   `SetTrainerService(index)`
    *   `SetUnit(unit)`
    *   `SetUnitAura("unitId", auraIndex[, filter])`
    *   `SetUnitBuff("unitId", buffIndex[, raidFilter])`
    *   `SetUnitDebuff("unitId", buffIndex[, raidFilter])`
*   **MessageFrame**: A frame that displays messages.
    *   `AddMessage("text", r, g, b, messageGroup, holdTime)`
    *   `Clear()`
    *   `GetFadeDuration()`
    *   `GetFading()`
    *   `GetInsertMode()`
    *   `GetTimeVisible()`
    *   `SetFadeDuration(seconds)`
    *   `SetFading(status)`
    *   `SetInsertMode("TOP" or "BOTTOM")`
    *   `SetTimeVisible(seconds)`
*   **Minimap**: A frame that displays the minimap.
    *   `GetPingPosition()`
    *   `GetZoom()`
    *   `GetZoomLevels()`
    *   `PingLocation(x, y)`
    *   `SetArrowModel("file")`
    *   `SetBlipTexture(texture)`
    *   `SetIconTexture(texture)`
    *   `SetMaskTexture(texture)`
    *   `SetPlayerModel("file")`
    *   `SetZoom(level)`
*   **Model**: A frame that displays a 3D model.
    *   `AdvanceTime()`
    *   `ClearFog()`
    *   `ClearModel()`
    *   `GetFacing()`
    *   `GetFogColor()`
    *   `GetFogFar()`
    *   `GetFogNear()`
    *   `GetLight()`
    *   `GetModel()`
    *   `GetModelScale()`
    *   `GetPosition()`
    *   `ReplaceIconTexture("texture")`
    *   `SetCamera(index)`
    *   `SetFacing(facing)`
    *   `SetFogColor(r, g, b[, a])`
    *   `SetFogFar(value)`
    *   `SetFogNear(value)`
    *   `SetGlow(..)`
    *   `SetLight(enabled[, omni, dirX, dirY, dirZ, ambIntensity[, ambR, ambG, ambB[, dirIntensity[, dirR, dirG, dirB]]]])`
    *   `SetModel("file")`
    *   `SetModelScale(scale)`
    *   `SetPosition(x, y, z)`
    *   `SetSequence(sequence)`
    *   `SetSequenceTime(sequence, time)`
*   **ScrollFrame**: A frame that can be scrolled.
    *   `GetHorizontalScroll()`
    *   `GetHorizontalScrollRange()`
    *   `GetScrollChild()`
    *   `GetVerticalScroll()`
    *   `GetVerticalScrollRange()`
    *   `SetHorizontalScroll(offset)`
    *   `SetScrollChild(child)`
    *   `SetVerticalScroll(offset)`
    *   `UpdateScrollChildRect()`
*   **ScrollingMessageFrame**: A frame that displays scrolling messages.
    *   `AddMessage("text"[, r, g, b[, id][, addToStart]])`
    *   `AtBottom()`
    *   `AtTop()`
    *   `Clear()`
    *   `GetCurrentLine()`
    *   `GetCurrentScroll()`
    *   `GetFadeDuration()`
    *   `GetFading()`
    *   `GetHyperlinksEnabled()`
    *   `GetInsertMode()`
    *   `GetMaxLines()`
    *   `GetNumLinesDisplayed()`
    *   `GetNumMessages()`
    *   `GetScrollOffset()`
    *   `GetTimeVisible()`
    *   `PageDown()`
    *   `PageUp()`
    *   `ScrollDown()`
    *   `ScrollToBottom()`
    *   `ScrollToTop()`
    *   `ScrollUp()`
    *   `SetFadeDuration(seconds)`
    *   `SetFading([isEnabled])`
    *   `SetHyperlinksEnabled(enableFlag)`
    *   `SetInsertMode("mode")`
    *   `SetMaxLines(lines)`
    *   `SetScrollOffset(offset)`
    *   `SetTimeVisible(seconds)`
    *   `UpdateColorByID(id, r, g, b)`
*   **SimpleHTML**: A frame that displays simple HTML.
    *   `GetFont(["element"])`
    *   `GetFontObject(["element"])`
    *   `GetHyperlinkFormat()`
    *   `GetHyperlinksEnabled()`
    *   `GetJustifyH(["element"])`
    *   `GetJustifyV(["element"])`
    *   `GetShadowColor(["element"])`
    *   `GetShadowOffset(["element"])`
    *   `GetSpacing(["element"])`
    *   `GetTextColor(["element"])`
    *   `SetFont(["element",] "path", height[,"flags"])`
    *   `SetFontObject(["element",] fontObject)`
    *   `SetHyperlinkFormat("format")`
    *   `SetHyperlinksEnabled(enableFlag)`
    *   `SetJustifyH(["element",] "justifyH")`
    *   `SetJustifyV(["element",] "justifyV")`
    *   `SetShadowColor(["element",] r, g, b[, a])`
    *   `SetShadowOffset(["element",] x, y)`
    *   `SetSpacing(["element",] lineSpacing)`
    *   `SetText("text")`
    *   `SetTextColor(["element",] r, g, b[, a])`
*   **Slider**: A frame that allows the user to select a value from a range.
    *   `Disable()`
    *   `Enable()`
    *   `GetMinMaxValues()`
    *   `GetOrientation()`
    *   `GetStepsPerPage()`
    *   `GetThumbTexture()`
    *   `GetValue()`
    *   `GetValueStep()`
    *   `IsEnabled()`
    *   `SetMinMaxValues(min, max)`
    *   `SetOrientation("orientation")`
    *   `SetStepsPerPage(value)`
    *   `SetThumbTexture(texture or "texturePath")`
    *   `SetValue(value)`
    *   `SetValueStep(value)`
*   **StatusBar**: A frame that displays a status bar.
    *   `GetMinMaxValues()`
    *   `GetOrientation()`
    *   `GetStatusBarColor()`
    *   `GetStatusBarTexture()`
    *   `GetValue()`
    *   `SetMinMaxValues(min, max)`
    *   `SetOrientation("orientation")`
    *   `SetStatusBarColor(r, g, b[, alpha])`
    *   `SetStatusBarTexture("file" or texture[,"layer"])`
    *   `SetValue(value)`

### LayeredRegion Derivatives

*   **Texture**: A region that displays a texture.
    *   `GetBlendMode()`
    *   `GetTexCoord()`
    *   `GetTexture()`
    *   `GetVertexColor()`
    *   `IsDesaturated()`
    *   `SetBlendMode("mode")`
    *   `SetDesaturated(flag)`
    *   `SetGradient("orientation", minR, minG, minB, maxR, maxG, maxB)`
    *   `SetGradientAlpha("orientation", minR, minG, minB, minA, maxR, maxG, maxB, maxA)`
    *   `SetRotation(angle, [,cx, cy])`
    *   `SetTexCoord(minX, maxX, minY, maxY or ULx, ULy, LLx, LLy, URx, URy, LRx, LRy)`
    *   `SetTexture("texturePath")`
    *   `SetColorTexture(r, g, b[, a])`
*   **FontString**: A region that displays text.
    *   `CanNonSpaceWrap()`
    *   `GetStringHeight()`
    *   `GetStringWidth()`
    *   `GetText()`
    *   `SetAlphaGradient(start, length)`
    *   `SetFormattedText("formatstring"[, ...])`
    *   `SetNonSpaceWrap(wrapFlag)`
    *   `SetText("text")`
    *   `SetTextHeight(pixelHeight)`

### Special

*   **WorldFrame**: The frame which is used to display 3D world itself.
*   **Templates**: Objects generated from XML that can be used as templates in Lua code.
## Secure Templates

SecureTemplates are a family of protected frame and button templates defined as part of FrameXML. Through attribute-based configuration, they allow addons to access some of the protected functionality. The templates are defined in `FrameXML/SecureTemplates.xml`, and functionality supporting them resides in `FrameXML/SecureTemplates.lua`.

*   Source: https://wowwiki-archive.fandom.com/wiki/SecureTemplates

| Template | Widget Type | Function |
| --- | --- | --- |
| SecureActionButtonTemplate | Button | Perform protected actions. |
| SecureUnitButtonTemplate | Button | Unit frames. |
| SecureGroupHeaderTemplate | Frame | Managing group members. |
| SecurePartyHeaderTemplate | Frame | Managing party members. |
| SecureRaidGroupHeaderTemplate | Frame | Managing raid group members. |
| SecureGroupPetHeaderTemplate | Frame | Managing group pets. |
| SecurePartyPetHeaderTemplate | Frame | Managing party pets. |
| SecureRaidPetHeaderTemplate | Frame | Managing raid group pets. |

### Example

```xml
<Ui>
 <Button name="MyThornsButton" inherits="SecureActionButtonTemplate" parent="UIParent">
  <Attributes>
   <Attribute name="type" type="string" value="spell"/>
   <Attribute name="spell" type="string" value="Thorns"/>
   <Attribute name="unit" type="string" value="player"/>
  </Attributes>
  <Size x="64" y="64"/>
  <Layers><Layer level="OVERLAY">
    <Texture name="$parentIcon" file="Interface\\Icons\\Spell_Nature_Thorns" setAllPoints="true" />
  </Layer></Layers>
  <Anchors><Anchor point="CENTER" /></Anchors>
 </Button>
</Ui>
```

## Protected Functions

Protected functions are API functions that have restrictions on their use. There are three main types:

*   Functions that addons can NEVER call.
*   Functions that addons cannot call IN COMBAT.
*   Functions that addons can only call from a hardware event (i.e. the user clicking a button), but not from OnUpdate/OnEvent handlers.

*   Source: https://wowwiki-archive.fandom.com/wiki/Category:World_of_Warcraft_API/Protected_Functions

This is a partial list of protected functions:

*   `ActionButtonDown(id)`
*   `ActionButtonUp(id)`
*   `AssistUnit("unit")`
*   `CameraOrSelectOrMoveStart()`
*   `CameraOrSelectOrMoveStop([stickyFlag])`
*   `CastShapeshiftForm(index)`
*   `CastSpell(index, bookType)`
*   `CastSpellByName(name[, onSelf])`
*   `ClearFocus()`
*   `FocusUnit("unit")`
*   `PetAggressiveMode()`
*   `PetAttack()`
*   `PetDefensiveMode()`
*   `PetFollow()`
*   `PetPassiveMode()`
*   `PetWait()`
*   `PlaceRaidMarker(index)`
*   `SetPartyAssignment("assignment", player)`
*   `SpellTargetUnit("unit")`
*   `StopAttack()`
*   `SwapRaidSubgroup(index1, index2)`
*   `TargetUnit("unit")`
*   `TogglePetAutocast(index)`
*   `ToggleSpellAutocast("spellName" | spellId, bookType)`
*   `TurnOrActionStart()`
*   `TurnOrActionStop()`

## XML Schema

In World of Warcraft addons, XML files are used to define User Interface elements. WoW defines its own schema in `FrameXML/UI.xsd`. In general, this schema has four kinds of elements:

*   **Document root**: Every file has `<Ui>` as its outer element.
*   **Widgets**: The main elements of the UI, such as `Frame` and `Button`.
*   **Widget properties**: Elements that define properties of a widget, such as `Size` and `Anchors`.
*   **Inline scripts**: Elements that contain Lua code to be executed at certain times, such as `OnLoad` and `OnClick`.

*   Source: https://warcraft.wiki.gg/wiki/XML_user_interface

### Example

```xml
<Ui xmlns="http://www.blizzard.com/wow/ui/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.blizzard.com/wow/ui/ ..\\FrameXML\\UI.xsd">
	<Frame name="MyAddonFrame">  <!-- a sample widget -->
		<!-- sample properties and children -->
		<Scripts>   
			<OnLoad function="MyAddonFrame_OnLoad" />
		</Scripts>
		<Layers>
			<Layer level="ARTWORK">
				<Texture name="MyAddonFrameTexture" />
			</Layer>
		</Layers>
	</Frame>  <!-- end of sample widget -->
</Ui>
```
