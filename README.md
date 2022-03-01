import numpy as np
import array

def palier_PP_1vote_max(nb_PT_team, nb_pers_team):
    nb_PT_PP = nb_PT_team//nb_pers_team
    paliers_av = 0
    palier_tranzi = 0
    paliers = 0
    nb_votes_PP = 1
    nb_pt_utilise = 0
    while nb_PT_PP >= paliers:
        palier_tranzi = paliers
        paliers = paliers + (paliers - paliers_av) + 4
        paliers_av = palier_tranzi
        nb_votes_PP = nb_votes_PP + 1
        nb_pt_utilise = paliers
    if nb_PT_PP < paliers: #On retire un vote par personne
        nb_votes_PP = nb_votes_PP - 1
        nb_pt_utilise = paliers_av
    nb_votes = nb_votes_PP * nb_pers_team
    reste = nb_PT_team - nb_pt_utilise * nb_pers_team #nombre de points qu'il reste
    vote_plus = 0 # initialisation
    while reste >= paliers: #calcule du nombre de vote qu'on peut faire en plus avec ce qu'il reste
        vote_plus = vote_plus + 1
        reste = reste - (paliers - paliers_av)
    vote_tot = nb_votes + vote_plus #somme des deux
    list_nb_votes = (np.ones(nb_pers_team)*nb_votes_PP).astype(int)
    list_nb_voix = (np.ones(nb_pers_team)*nb_pt_utilise).astype(int)
    for i in range(vote_plus): #ajout des votes en plus
        list_nb_votes[i] = nb_votes_PP + 1
        list_nb_voix[i] = paliers
    return [list_nb_votes, list_nb_voix, [paliers], [paliers_av],[vote_plus]]
    
    
    
def equilibrage(nb_PT_team, nb_pers_team, nb_vt_equi):
    nb_points_PV = nb_PT_team // nb_vt_equi
    infos_PV = palier_PP_1vote_max(nb_points_PV, nb_pers_team)
    nb_pt_utilise_tot = sum(infos_PV[1][:])*nb_vt_equi
    reste = nb_PT_team - nb_pt_utilise_tot
    vote_plus = 0
    while reste >= infos_PV[2][0]:
        vote_plus = vote_plus + 1
        reste = reste - (infos_PV[2][0] - infos_PV[3][0])
    nb_voix_fin_PP  = np.zeros([nb_pers_team, nb_vt_equi],int)
    nb_votes_fin_PP = np.zeros([nb_pers_team, nb_vt_equi],int)
    print(nb_voix_fin_PP)
    print(infos_PV)
    for i in range(nb_vt_equi):
        nb_voix_fin_PP = np.append(nb_voix_fin_PP, infos_PV[1][:])
        nb_votes_fin_PP = np.append(nb_votes_fin_PP, infos_PV[1][:])
    print(nb_votes_fin_PP)
    L=0
    C=infos_PV[4][0]
    for i in range(vote_plus):
        nb_voix_fin_PP[L][C] = infos_PV[2][0]
        nb_votes_fin_PP[L][C] = nb_votes_fin_PP[L][C] + 1
        print(nb_voix_fin_PP)
        L = L + 1
        if L==nb_pers_team:
            L = 0
            C=C+1      
    return [nb_voix_fin_PP, nb_votes_fin_PP]
