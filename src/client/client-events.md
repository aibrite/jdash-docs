#Client Events
 
<html>
			<hr>
				<table cellspacing=0 border=1>
                <thead>
                    <tr>
						<th style=min-width:50px>Event Name</td>
						<th style=min-width:50px>Event Source</td>
						<th style=min-width:50px>Cancellable</td>
						<th style=min-width:50px>Can Bubble</td>
						<th style=min-width:50px>Prevent Default Action</td>
					</tr>
                </thead>
				<tbody>
                	<tr>
						<td style=min-width:50px>drag-started</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>Prevents Dragging</td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet:drag-started</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>Prevents Dragging</td>
					</tr>
					<tr>
						<td style=min-width:50px>drag-zone-entering</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>Prevents Animation Of Drop Zone Indicator</td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet:drag-zone-entering</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>Prevents Animation Of Drop Zone Indicator</td>
					</tr>
					<tr>
						<td style=min-width:50px>drag-zone-entered</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet:drag-zone-entered</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>drag-zone-left</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet:drag-zone-left</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>drop-to-zone-starting</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>Prevents Dropping (Reverts Back To Original Position)</td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet:drop-to-zone-starting</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>Prevents Dropping (Reverts Back To Original Position)</td>
					</tr>
					<tr>
						<td style=min-width:50px>drop-to-zone-end</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet:drop-to-zone-end</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet-module:drag-started</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>Prevents Dragging of Dashlet Module</td>
					</tr>
					<tr>
						<td style=min-width:50px>after-place</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>jdash:dashlet.after-place</td>
						<td style=min-width:50px>document</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px></td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet-removing</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>Prevents Removing Of Dashlet</td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet:dashlet-removing</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>Prevents Removing Of Dashlet</td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet-removed</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>dashlet:dashlet-removed</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>viewmode-change</td>
						<td style=min-width:50px>layout</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>attributeChanged</td>
						<td style=min-width:50px>All Jdash Created Elements</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>jdash:component.created</td>
						<td style=min-width:50px>window</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>state-change</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>layout-ready</td>
						<td style=min-width:50px>dashboard</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>execute-configuredashlet</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>Prevents Opening Configurator</td>
					</tr>
					<tr>
						<td style=min-width:50px>execute-setdashlettitle</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>Prevents Activating Dashlet Title Prompt</td>
					</tr>
					<tr>
						<td style=min-width:50px>execute-clonedashlet</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>Prevents Cloning Dashlet</td>
					</tr>
					<tr>
						<td style=min-width:50px>execute-removedashlet</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>Prevents Removing Action</td>
					</tr>
					<tr>
						<td style=min-width:50px>after-configuredashlet</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>after-setdashlettitle</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>after-clonedashlet</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>after-removedashlet</td>
						<td style=min-width:50px>dashlet</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>execute-closeeditor</td>
						<td style=min-width:50px>dashlet-editor</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>after-closeeditor</td>
						<td style=min-width:50px>dashlet-editor</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>before-setconfig</td>
						<td style=min-width:50px>dashlet-editor</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
					<tr>
						<td style=min-width:50px>after-setconfig</td>
						<td style=min-width:50px>dashlet-editor</td>
						<td style=min-width:50px>false</td>
						<td style=min-width:50px>true</td>
						<td style=min-width:50px>-</td>
					</tr>
                    </tbody>
				</table>
				<hr> 
</html>